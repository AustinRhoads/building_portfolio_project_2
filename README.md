

Between 1971 and 1998, John Shepherd using scientific equipment of his own design, beamed album after album into space in an attempt to contact aliens. Among the albums sent out were _Science Fiction_ by Ornette Coleman and _Zombie_ by Fela Kuti and Africa 70. I don’t know why it just makes me happy to think that aliens are listening to free-jazz and funky music. If I heard Fela Kuti playing next door, I might want to stop by and say hello. So for my Sinatra portfolio project I made an MVC structured web app called “UFO Tracker” because… well, why not? Users can post and review encounters they have with extraterrestrials. These are the main three concepts which grabbed my focus throughout the build.


## MVC

Model View Controller is a concept that has been around since the 70’s. Basically its a separation of concerns where the **models** handle the data, the **views** handle the place to display the data and the **controllers** organize and tie in all the data to the views. People love to make analogies for this. Mine would be something like this…

Picture yourself wandering around downtown Austin, TX and you happen upon a smoke-filled jazz bar.

The **music** the band is playing and the **booze** at the bar are the **models/data**, 

the **bar stool** and the **stage** are the **views**,

and the **stage manager**, **bar tender** and **doorman** are the **controllers**.

The models for my app had to be simple enough in concept and reflect real world objects that interact with each other. First, I built the user objects whose attributes are username,  email, password, and boolean answers for the questions, “are you a member of the military?” (```is_military?```), and “are you a member of law enforcment?” (```is_law_enforcement?```). Next, I built the encounter class which has the attributes name, date, location, description, and `kind` (as in, “was this a close encounter of a first, second or third kind?”).
The more attributes I give the encounter the more search options I have later on.

I don't want to rely on user input, so to prevent an error when signing in as "Username" when the database has it saved as "username", or "UserName" I created a special method that is encluded in a shared module. This was the ```ci_find``` or “case insensitive” find method.


```scope :ci_find, lambda { |attribute, value| where("lower(#{attribute}) = ?", value.downcase).first }```

I also use this in my ```slugifiable``` methods so I can ```downcase``` when making a slug and ```ci_find``` anything “slugged”.

    module Slugifiable

        module ClassMethods
            def find_by_slug(slug)

                name = slug.gsub("-", " ")

                if self == User
                    self.ci_find('username', name)
                else
                    self.ci_find('name', name)
                end
            end
        end

        module InstanceMethods
            def slug
                if self.class == User
                    self.username.downcase.gsub(/\s/, "-")
                else
                    self.name.downcase.gsub(/\s/, "-")
                end
            end
        end
    end
  

I might have had a little too much fun with the views. NASA has all these great hi-resolution photos generated from Hubble and layered from different spectrums like ultraviolet and infrared light which are public domain and just stunning.

![habital](./pd36-1-gsfc_20171208_archive_e000842.jpg)

I just couldn’t resist creating a parallax background from an image of a galaxy cluster. Lots of fun! However the real magic in the views is how variables are created in the controller and referenced in the views. This makes it so each view only deals with data appropriate to it.

The controllers classes that join the models and the views together utilize what is known as RESTful (representational state transfer) routes.



## RESTful routes

Different pages on a site are actually considered different **states** of that application. As we click links to navigate to different pages on a site what occurs is a **state transition**. **RESTful** routes is a conventional way of linking **CRUD** (create, read, update, delete) actions with **HTTP verbs** (get, post, patch, delete). 
So when we are signing in we are in one state, and when we are creating a post we are in another state of the app. Each state is responsible for retrieving it’s own data. Inside the controllers we define these routes and what data and views are accessible. For each part of the process there is a conventional way of doing things. So for instance if I am logged in and looking at all the reported UFOs and I want to report a UFO myself I click on the link “Report a UFO” and a **state transfer** begins. Because this is related to managing my encounters data the link is defined in the EncountersController class.

    get '/encounters/new' do
    	erb :'encounters/new'
    end


 This sends a **get** **HTTP request** for the ‘encounters/new’ view which displays a form to create a new encounter. Once the form is submitted this sends a **post HTTP request** to create a new instance of an encounter object defined as such:


    post '/encounters' do
    	@encounter = Encounter.new(params)
    	@user = current_user
    	@user.encounters << @encounter
    	@encounter.save
    	redirect "/encounters/#{@encounter.id}"
    end


A user *has_many* encounters and an encounter *belongs_to* a user. 
The method ```current_user``` is used to ensure the user can only create or publish a post in their own name. The beauty of this is how it allows us to tie the functionality of Ruby objects to the content on our web app. Something else to consider here is that for the app to work properly a user has to be signed in. To keep someone who isn’t logged in from using the app I created a special method with the appropriate name ```redirect_if_not_signed_in(proc)```.

    def redirect_if_not_logged_in(proc)
    	if !logged_in?
        redirect "/login"
        else
        proc.call
        end
    end
    
The ```proc``` is the block of code I would normally run inside the route. If the user is logged in it runs the ```proc``` if not it redirects to the “/login” route. Each route gets rewritten as follows:

    get '/encounters/new' do
        proc = Proc.new{
            erb :'encounters/new'
        }
        redirect_if_not_logged_in(proc)
    end

and:

    post '/encounters' do
        
        proc = Proc.new {

            @encounter = Encounter.new(params)
         @user = current_user
         @user.encounters << @encounter
         @encounter.save
         redirect "/encounters/#{@encounter.id}"

        }
        redirect_if_not_logged_in(proc)
    end

## Google API

Ultimately I wanted the functionality of my web app to go beyond the CRUD format which is why I embedded a few Google APIs. I used the [Google Maps API](https://developers.google.com/maps/documentation) to display each reported UFO incident on the ‘encounter/index’ page and the [Google Geocode API](https://developers.google.com/maps/documentation/javascript/examples/places-autocomplete-addressform) to have an auto-fill location input. This also allowed me to get a latitude and longitude for the encounter from the selected location, which I then used to create the marker displayed on my embedded Google map. This was just so much fun to see come together and in the end it just makes the app feel so much more powerful.


## Conclusion

