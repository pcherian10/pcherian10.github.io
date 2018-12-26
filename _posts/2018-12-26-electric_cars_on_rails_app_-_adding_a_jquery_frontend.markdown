---
layout: post
title:      "Electric Cars on Rails App - Adding a JQuery Frontend"
date:       2018-12-26 23:07:54 +0000
permalink:  electric_cars_on_rails_app_-_adding_a_jquery_frontend
---


The next step toward modernizing the app was the addition of a Javascript front-end. I used JQuery to save time in creating the app as well as improving the readability of the code. The main change atleast with regard to optics was condensing the app's functionality into one view and one controller as a result of using AJAX. Lastly, implementing  the `respond_to` feature to convert backend Rails data to the JSON format, was integral to the communication between the frontend and backend. [Here](https://github.com/pcherian10/electric-car-rentals-rails-project-v-000/tree/jquery-frontend) is the current project under development. 

The main view of the app is the `users/show` page, while the asset pipeline houses the `electric-cars.js` and `application.css` files which are used to render the frontend. The backend remains mostly the same, with the exception of `serializer` files, creation of new form partials and modifications to the `bookings_controller`

## Key JS Components

### AJAX

From using several views and routes, I was able to switch to use just one view and one route,  thanks to using JS and AJAX. AJAX allows for sending `GET`, `POST` and `PUT` / `PATCH` requests to the server and retreive and show the information in the current view while in the same route. In the previous Rails app, I had redirects to each view, but with AJAX, I just had each form render in specific `<div>'s` wrappers and segments, initiated by the respective buttons on the page. A typical page 


### Prototype

Using JS model objects helped make form rendering more abstract. Specifically, this facilitated reliable rendering of bookings list everytime, a new bookng was created, or if an existing booking was updated (or deleted). By simply calling `createUserDetailsHTML` on a`UserDetails` object, the form was rendered. A UserDetails object has all the information of the user i.e, the cars and bookings tied to him/her. The attribute of `bookings` were used to generate the form for the View/Edit Bookings button.

> `class UserDetails {
	constructor(obj) {
		this.userName = obj.name
		this.bookings = obj.bookings
		this.userCars = obj.cars
	}
}`

Then function was defined for the prototype per the below.

>`UserDetails.prototype.createUserDetailsHTML = function () {}`


### Setting Up and Consuming JSON

This was my first brush at creating, using and consuming JSON. The main driver of creating JSON was the following code:

>`respond_to do |f|
        f.html {redirect_to user_path(@booking.user)}
        f.json {render :json => @booking.user}
      end`
			
This was used in the `bookings_controller`, where it retreived data from the table converted it to JSON, and ensured it was ready to transmit to JS.  Almost every AJAX request was sent to specific url that returned these JSON objects, which was then consumed to render forms. This was a critical piece of code that allowed the app to function. 


#### Serializers

Serializers were used to ensure that key Model relationships were maintained. It's simple to set up and ensures that JSON data is properly structured, specifically with regard to key: value relationships and nested keys within objects.

> `class BookingSerializer < ActiveModel::Serializer
  attributes :id, :start_date, :end_date, :car
  belongs_to :user
  belongs_to :car

end`

This was also a key piece of code that allowed for the JSON to be created and structured properly with ease.

### Bootstrap

I've started incorporating Bootstrap for styling the app. Key things I've implemented include using a modal to display and complete forms and adding basic CSS background animations and images. I've just started learning it's features but expect to use it more extensively to improve the look and feel of the app over time.

## Conclusion

Adding the JS frontend was a step-change to the improve the user experience. Rather than having different routes for each function, which is more time consuming and disrupts the the flow of thought, JS, AJAX and accessing user data via JSON is substantially more seamless. I plan to continue to build out the app to improve it's look and feel as well as other functionality that improves the app. 



