# Slots

To make the skill more useful, we need to allow it to be more flexible and have a better understanding of the User's requests.

To achieve this we need to introduce slots.

The first useful slot we can have is the date on which we want to watch a movie, so Alexa can understand requests like _I would like to see a movie **tomorrow**_

This is pretty simple as dates are one of the built-in [slot types provided](https://developer.amazon.com/en-US/docs/alexa/custom-skills/slot-type-reference.html)

To improve autosuggestions and accessing the right properties, we updated the _IModel_ interface to use the type `v1.skill.interactionModel.Intent`.

Following the type definition adding the slot to capture the date was as easy as adding the proper element in the property _slots_, and a sample that made use of it.

After updating the interaction model with the new addition and building the model of our skill, we are able to understand requests for specific dates provided by our user.

## Help refining results

There are moments in which the list of movies to offer is too lengthy and simply citing all the movies makes for an awful interaction. It's time to enable some [custom slot](https://developer.amazon.com/en-US/docs/alexa/custom-skills/create-and-edit-custom-slot-types.html) that would allow the user to filter by type of movie (dubbed or subbed), and the language of the movie.

To define custom slots we extend our _IModel_ interface to accept an array of `v1.skill.interactionModel.SlotType`. This type will help us extend the internationalized model to support custom slots for language and type.