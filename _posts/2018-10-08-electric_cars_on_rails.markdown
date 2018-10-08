---
layout: post
title:      "Electric Cars on Rails"
date:       2018-10-08 20:34:59 +0000
permalink:  electric_cars_on_rails
---

The future is looking increasingly electric. With almost every major car manufacturer looking to enter the electric market or expanding their existing offerings, products and services supporting the market will have to also increase. One idea that came to mind was the potential increase in appetite for renting electric cars as opposed to ownership, which actually appears to irk millennial consumers, even if they are for electric cars. The economics at play here are pretty simple; one would rather pay a smaller monthly fee for access to a car for a select number of times a month rather than paying the onerous monthly payment associated with owning a car. I think at first brush this could be achieved with economics of scale when considering a whole fleet of vehicles, further enhanced by the potential of autonomous driving capability — although that’s a bridge to cross later. While starting up this business would require a more deep analysis of current business fundamentals and the existing market for the product, only an idea is enough to create a rails app that might or might not feed into being the company’s future website! Here is the link to the finished electric car rental business rails app.

## Basic Concept and Walkthrough
Here is a quick run through the barebones back-end of the app. It’s essentially a booking app — allowing someone to book a resource for a period of time. Currently, I’ve decided not to include front-end features in lieu of just showing the key back-end functionality, but am currently adding a bootstrap front-end.

## Key Models and Associations
The basic structure of the app follows an MVC structure. In a nutshell, the user is the renter, the car is the resource being rented. Those are both stand alone table entities. In the middle, the booking entity acts as the join-table to create the appropriate associations, with attributes of car_id and user_id that are used to create the belongs_to associations with car and user. Thus, the overall associations are that a user has_many bookings and has_many cars through: bookings. A car has_many bookings and many users through: bookings. The schema file is shown below with the relevant models and attributes.

```
Users
    t.string "name"
    t.string "email"
    t.boolean "admin", default: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.string "provider"
    t.string "uid"
    t.string "oauth_token"
    t.datetime "oauth_expires_at"
    t.string "password_digest"
Bookings
    t.date "start_date"
    t.date "end_date"
    t.integer "user_id"
    t.integer "car_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false

Cars
    t.string "name"
    t.integer "top_speed"
    t.integer "range"
    t.integer "charge_time"
    t.integer "price_per_day"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false

```
 
### Key Features

The video above should have made clear the app’s basic functionality. The features I’d like to highlight are the Google Authentication, important validations, and the creation and use of the admin_controller utilizing the namespace and scope rails methods to make possible to view stats and add cars by the owner of the business.

### Google Authentication

I chose this OAuth strategy mainly for its relative simplicity (especially compared to Facebook). While overall rather simple, one bump in the road ensuring that it worked in conjunction withbcrypt gem. This was accomplished by adding a condition that evaluated whether omniauth.auth was triggered in the sessions_controller, as seen below.

```
class SessionsController < ApplicationController
def new
    @user = User.new
  end
def create
    if auth
      user = User.from_omniauth(request.env["omniauth.auth"])
      session[:user_id] = user.id
      redirect_to user_path(id: user.id)
    else
      @user = User.find_by(name: params[:user][:name])
       if @user && @user.authenticate(params[:user][:password])
          session[:user_id] = @user.id
          redirect_to user_path(@user), notice: "Welcome to E-Cars Rentals!"
       else
          flash[:notice] = "Incorrect username and/or password combination."
          redirect_to signin_path
       end
    end
  end
def destroy
    session[:user_id] = nil
    redirect_to root_url
  end
private
def auth
    request.env['omniauth.auth']
  end
end

```

Without that conditional, the app would throw an error saying that the user is invalid. Moreover, a class method was created in the user.rb file that filled in OAuth attributes as well as the password_digest which cannot be left blank due to the fact that has_secure_password has a presence validation that cannot be disabled. To bypass this, a random password generator was used to fill the attribute, as seen below.

```
def self.from_omniauth(auth)
    where(provider: auth.provider, uid: auth.uid).first_or_initialize.tap do |user|
      user.provider = auth.provider
      user.uid = auth.uid
      user.name = auth.info.name
      user.oauth_token = auth.credentials.token
      user.oauth_expires_at = Time.at(auth.credentials.expires_at)
      user.password = SecureRandom.urlsafe_base64
      user.save!
    end
  end
```

That was the main issue regarding the Authentication, the above modifications fixed the problems. The feature now works perfectly and adds an additional level of credibility to the app.

### Important Validations

The main and only inputs required for the app is the actual booking dates for cars. Thus, the obvious validations are that the dates make sense (i.e, they are in the future and ‘End’ is greater than ‘Start’) and that a user can’t accidentally double book a car that is already booked for a specific date. The former validation was rather simple — a function called valid_date that ensured that end_date attribute was larger than start_date attribute. The latter was more complex, as a result of having to iterate through all the bookings to ensure that there wasn’t a duplicate. Further, if the car was already booked, I needed to display the dates that the car is not available. This was possible through two functions for the booking model, as shown below.


```
def duplicate_booking
    Booking.all.each do |booking|
      if booking.id != self.id
        if booking.car == self.car
          if self.start_date <= booking.end_date && self.start_date >= booking.start_date || self.end_date <= booking.end_date && self.end_date >= booking.start_date
            return true
          end
        end
      end
    end
  end
def return_available_dates
    Booking.all.each do |booking|
      if self.start_date <= booking.end_date && self.start_date >= booking.start_date || self.end_date <= booking.end_date && self.end_date >= booking.start_date
          return "This car is not available between #{booking.start_date} and #{booking.end_date}. Choose another time! Thanks."
      end
    end
  end
	
```

In essence, the first function ensures that no date falls in between an existing booking’s dates. The second does the same iteration but returns the dates in a message so that the user is alerted. This works well for now to resolve the issue.

### The Admin Feature

The final feature of the app is providing access to the owners of the app. The current version of the app assumes that the admins are owners of the business and not peers who may not add cars. The option is made available during the sign up process via a checkbox. While this is not “true” authentication, it would be imagine a case where the admin access user is only shown to pre-selected users by the owner. Getting down to the brass tacks — the main functions available via the page are to view all bookings and some related stats and manage the car inventory — specifically, a mini-CRUD app inside modify and update details.

This was possible added due to the functionality given by the namespace routing option in Rails. The admin controller almost feels like an app inside of the app, with copies of the bookings and cars controllers, that are distinct and only available to an admin. The code below details the setup:

```
namespace :admin do
    resources :bookings, :only => [:index, :show]
    resources :cars, only: [:new, :create, :edit, :update, :destroy, :show, :index]
    resources :home, only: [:index]
  end
```

The namespace route customized the admin name for the route. Inside, the pages were essentially recreated with customized routes for booking and cars, with specific functionality to admins.

Lastly, while not as crucial a feature, scope was also a noteworthy feature as it was used to simplify calling a function on an object, specifically, to just say booking.user.admin which would return either true if admin attribute was true. Overall, this increased the code’s readability, even though it only replaces the modest labor of just adding class function. The actual code is below:

```
scope :admin, -> { where(admin: true) }
```

### Future Steps

The application thus far represents the first part of the web apps completions. I’ve encapsulated the main barebones functionality that a really cool, awesome future app will possess. I plan to build out the front-end for this app, as well as improving the back-end UI and UX over time. For now, this has been a fantastic endeavor, from which I’ve basically become extremely comfortable with planning, running and debugging a Rails app. There will most likely be a second installment to this post detailing my efforts on the front-end aspect — especially as I delve into Javascript. Stay tuned!
