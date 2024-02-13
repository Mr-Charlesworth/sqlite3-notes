# Extra Challenges

- The main feature of relational databases comes from creating relationships between tables through joins and constraints
- So far we have made a very basic collector that stores users and their locations.
- Here are some challenges to expand this basic app to use some [key contrainsts](https://www.sqlite.org/foreignkeys.html) and [joins](https://www.sqlitetutorial.net/sqlite-join/)
- This is intended to refresh your understanding of basic database concepts that you did in semester 1 and how we can do this stuff in flask.
- It doesn't have to be pretty, aim for the functionality.
- Don't worry about implementing a login system, let's just let anyone do this stuff. I.e. let's pretend we are some sort of "superuser".

## Hobbies

- Give each user a button that goes to a "Hobby" page for that user
- The hobby page should show a form at the top to add a hobby for that user
- Each hobby should have a name and a description
- The added hobbies are listed below the form
- Each listed hobby should have buttons to delete and edit.

## Friends

- Each listed user should have a link to a "Friends" page for that user
- From the "Friends" page, it should be possible to select any other user and send them a friend request
- Pending friend requests from the user should be listed
- Pending friend requests to the user should be listed where it is possible to accept the request
- Confirmed friends should be listed where it should be possible to end the friendship.
