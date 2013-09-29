# 1. In "Laying the foundations", Ryan covers a number of steps that he'll walk through. What are they?
create the foundations of the subscription engine.

*create a brand new engine using the rails plugin new generator, which will create our engine and create a dummy application inside the engine. 
	-The dummy application inside the engine will be used to test the engine’s functionality, pretending for the duration of the tests that the engine is actually mounted inside a 	real application.
	-Having this in an engine and not an application allows us to utilize the same functionality in other applications later on and keep the code subscriptions separate from other 	code.
*write the first feature for the engine: account sign up. 
	-By having accounts within the engine, it provides the basis for scoping resources for whatever application the engine is embedded into.
*set it up so that accounts will be related to an “owner” when they are created.
	-The owner will (eventually) be responsible for all admin-type actions within the account. 
	-We’ll be using Warden to manage the authentication proceedings of the engine.
*Write another group of features which will handle user sign in, sign up and sign out.
	-Segue into our work in the next chapter, in which we’ll actually do some scoping!

# 2. What's a Rails Engine?

Engines can be considered miniature applications that provide functionality to their host applications. A Rails application is actually just a "supercharged" engine, with the Rails::Application class inheriting a lot of its behavior from Rails::Engine.

Therefore, engines and applications can be thought of almost the same thing, just with subtle differences, as you'll see throughout this guide. Engines and applications also share a common structure.

Engines are also closely related to plugins where the two share a common lib directory structure and are both generated using the rails plugin new generator. The difference being that an engine is considered a "full plugin" by Rails as indicated by the --full option that's passed to the generator command, but this guide will refer to them simply as "engines" throughout. An engine can be a plugin, and a plugin can be an engine.

3.What command do we need to write to create a new rails plugin, with a dummy application and without an isolated namespace?
rails plugin new <name of plugin> \ --dummy-path spec/dummy --skip-test-unit

4.What's the first feature being built? What steps need to be taken ("tested") to verify it's working?
Account sign up.

First Run Spec: rspec spec/features/accounts/sign_up_spec.rb 

We should see this output:
Failure/Error: visit subscribem.root_path 
NoMethodError:
  undefined method `root_path' for #<ActionDispatch::...>

The subscribem method inside the spec is an ActionDispatch::Routing::RoutesProxy object which provides a proxy object to the routes of the engine. Calling root_url on this routing proxy object should return the root path of the engine. Calling root_path without the subscribem prefix – without the routing proxy – in the test will return the root of the application.

Run Spec: rspec spec/features/accounts/sign_up_spec.rb 

The reason that this test is failing is because we don’t currently have a root definition inside the engine’s routes. So let’s define one now inside config/routes.rb like this:

Subscribem::Engine.routes.draw do 
root :to => "dashboard#index"
end