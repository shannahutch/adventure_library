# Adventure Library

## WDI mini-project for 2/13/2014

This is your start for making your node in our distributed text adventure library.

Each of you will build a website which allows a user to view text adventures, click through the pages, and add text adventures to the database.  Here's the twist.  A user on your site will also be able to put in the name of another text adventure library, and your site will retrieve all the text adventures which were made in that library, and all of the other libraries that that library knows about.  Conversely, your library will make available via JSON endpoints all the text adventures which are locally made, and a list of all the servers which your server knows about.

## Subjects covered in this project:

* Testing
* Relationships (has_many and belongs_to)
* Using JSON APIs
* Responding with JSON
* Background workers
* Controllers and Views

## The HTML user interface

A user is able to go to a form to build a NEW text adventure.  Submitting this form will CREATE an adventure.  When an adventure is created, it will be automatically assigned a guid, a Global Unique identifier, using SecureRandom. (SecureRandom.urlsafe_base64(10))

A user can EDIT and UPDATE an adventure by changing the title, deleting pages, or adding pages.

A user can view an INDEX of adventures and click through to a specific adventure.

A user can be SHOWn an adventure.  This will start them on the page for that adventure which has the name "start".  I.E, `adventure.pages.where(:name => "start")`.  The text for that page will be shown, with any connections between pages in that text turned into links in the HTML.  Connections between pages are in the following format: [[link text|linked_to_page_name]].  See the section "Displaying the text of a Page" for information on how to turn these double bracket links into anchor tags.

A user can click through links on a page to go to connected pages.

A user can go to a form to enter in the URL of another library server.  When a user submits that form, it will start the library scraping process.

## The JSON interface

A JSON client can GET '/libraries' to get a list of libraries which the current server knows about in JSON format.

A JSON client can GET '/adventures' to get a list of adventures created on the local library server, with their pages.

## The Library scraping process

When a user submits a new library URL, "alibrary.com", this should start the library scraping process.  Your library will save that server ("alibrary.com") to the database, then set off jobs to GET all the adventures from that library ("alibrary.com/adventures.json") and GET all the libraries ("alibrary.com/libraries.json") which that library knows about.

In the scraping process, if other servers are returning "id" fields from their local database, do not use their ids to save data to your database.  They might conflict with the ids of data in your database.


BONUS: After a library has been added to the server, use Heroku Scheduler or another scheduling system like Sidetiq to retrieve updated adventures from that server on a regular basis.  Overwrite the correct adventure in your database using the GUID.

## Displaying the text of a Page

We can use Ruby's String#gsub functionality to substitute one substring for another throughout the string.  We use a regular expression to detect the pattern of the link in double brackets, then in the passed block, return the HTML link (anchor tag) which we want to replace it with.

We've provided a method `linkify_page` in PagesHelper which takes a page and returns the text with the double bracket links replaced with anchor tags.  It assumes you have not changed the routes, so a page should be accessible at:

`/adventures/:adventure_id/pages/:page_id`

## The schema

Adventure:
  *  has many Pages
  *  belongs to a Library.  If library_id is nil, the adventure is local.
  *  has a title (string)
  *  has an author (string)
  *  has timestamps
  *  has a GUID (string)

Page:
  * belongs to an Adventure
  * has a name (string)
  * has text (text)

Library:
  * has many Adventures
  * has a URL


