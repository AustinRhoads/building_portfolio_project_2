

Between 1971 and 1998, John Shepherd using scientific equipment of his own design, beamed album after album into space in an attempt to contact aliens. Among the albums sent out were _Science Fiction_ by Ornette Coleman and _Zombie_ by Fela Kuti and Africa 70. I don’t know why it just makes me happy to think that aliens are listening to free-jazz and funky music. If I heard Fela Kuti playing next door, I might want to stop by and say hello. So for my Sinatra portfolio project I made an MVC structured web app called “UFO Tracker” because… well, why not? Users can post and review encounters they have with extraterrestrials. 


## MVC

Model View Controller is a concept that has been around since the 70’s. Basically its a separation of concerns where the **models** handle the data, the **views** handle the place to display the data and the **controllers** organize and tie in all the data to the views. People love to make analogies for this. Mine would be something like this…

Picture yourself wandering around downtown Austin, TX and you happen upon a smoke-filled jazz bar.

The **music** the band is playing and the **booze** at the bar are the **models/data**, 

the **bar stool** and the **stage** are the **views**,

and the **stage manager**, **bar tender** and **doorman** are the **controllers**.

The models for my app had to be simple enough in concept and reflect real world objects that interact with each other. So instead of having a UFO object that someone could report I decided it was more robust of a concept to use the Encounter object. An encounter could have or not have a UFO. It could also be a tiny alien who heals your paper cuts with his glowing finger or whatever.

I might have had a little too much fun with the views. NASA has all these great hi-resolution photos generated from Hubble and layered from different spectrums like ultraviolet and infrared light which are public domain and just stunning. I just couldn’t resist creating a parallax background from an image of a stellar nursery. Lots of fun! However the real magic in the views is how variables are created in the controller and referenced in the views. This makes it so each view only deals with data appropriate to it.

The controllers classes that join the models and the views together utilize what is known as RESTful (representational state transfer) routes.



## RESTful routes

Different pages on a site are actually considered different **states** of that application. As we click links to navigate to different pages on a site what occurs is a **state transition**. **RESTful** routes is a conventional way of linking **CRUD** (create, read, update, delete) actions with **HTTP verbs** (get, post, patch, delete). 
The beauty of this is how it allows us to tie the functionality of Ruby objects to the content on our web app.

## Google API

Ultimately I wanted the functionality of my web app to go beyond the CRUD format which is why I embedded a few Google APIs. I used the Google Maps API to display each reported UFO incident on the ‘encounter/index’ page and the Google Geocode API to have an auto-fill location input. This also allowed me to get a latitude and longitude for the encounter from the selected location, which I then used to create the marker displayed on my embedded Google map. This was just so much fun to see come together and in the end it just makes the app feel so much more powerful.

## Conclusion

