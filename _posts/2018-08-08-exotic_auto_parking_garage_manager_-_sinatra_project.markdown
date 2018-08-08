---
layout: post
title:      "Exotic Auto Parking Garage Manager - Sinatra Project"
date:       2018-08-08 23:27:46 +0000
permalink:  exotic_auto_parking_garage_manager_-_sinatra_project
---

This app is an attempt to answer a potential problem for a recent start-up idea I had - an exotic car/alternative vehicle rental service. Exotic cars and motorcycles are a passion of mine and thought I could use this opportunity to marry it with my interest and knowledge of coding. While the actual business concept is simple, one of its complicated aspects is inventory management. The goal of the app is to simplify inventory management of any and all garages, and the autos inside them.

The file structure follows the conventions of a typical MVC app. It has three models - user, auto, and garage placed in the models folder. Each model.rb file inherits ActiveRecord functionality and establishes the relational association. In this case, a User has many Garages and each Garage has many Autos. The reverse associations are also true. Each Auto belongs to a Garage and each Garage belongs to a User.

The controllers folder houses each of the associated controllers with respective models. Each controller.rb file has the associated RESTful routes with code that executes the intended purpose. The views folder has the .erb files for each of the models and contains all the forms for every specific action. While the Gemfile needs no explanation, notable new gems include Bcrypt for encryption and shotgun, which allows for temporary servers to be set up. Tux was greatly helpful in the debugging process and seeing Ruby objects run in real time. Lastly, the rakefile was also set up to run all the ActiveRecord migrations.

SQL is used as the the database and is managed using ActiveRecord methods. The attributes (columns) for each model are as follows. The User has a username and password. The Garage has a name, capacity and a foreign user_id attribute as it has a belongs_to relationship with the User. The Auto has a name and manufacturer as well as an user_id attribute, because it has a belongs_to relationship with a Garage.

The flow of the app starts with a user logging in or signing up. The login process uses Bcrypt for encryption and the ActiveRecord's authenticate function for validation. Inside the users_controller are the RESTful routes for the 'signup',  '/login' and 'logout'. These routes use Sinatra's sessions method to keep track of a user logging in and out. The sessions functionality was established in the 'applications_controller' and contains the basic index route as well as helper functions to make sure users are logged in and only logged in users can access any information. These include logged_in? which returns falsey if the :user_id parameter is not in the current session. The views function

> `  def logged_in?
>       !!session[:user_id]
>     end
> `

The current_user function returns the appropriate User object if there is a match of the user_id in the session array. 

Once the user is logged in, the landing page displays all their garages. If they are a first time user, they have the option of creating a new garage. The garages controller houses all the routes that the user may want to go depending on the action. For garages, the user may want to edit (edit.erb) attributes of the Garage model i.e, the name and capacity, create (new.erb) or delete a garage( via delete button in show.erb), or show details of  a specific garage (show.erb).

Upon creating the first garage, the next step is to add an auto to the garage. The Auto model also has the functions similar to the Garage model. The auto index.erb page shows all the autos in a specific garage. The user has the option to create (new.erb) a new auto with attributes of the name of the manufacturer and make of the car and assign it to an existing garage. Additionally, they can edit, i.e, change the name/manufacturer/assign it to different garage. This is accomplished it via ActiveRecord's .update method. The show.erb method is used to show specific details of any given Auto object.

The final product allows the user to keep track of all the garages and the autos inside them. A very busy garage owner with multiple garages and autos can check up on them at any time. I was thrilled to see it working and am happy with the initial product.








