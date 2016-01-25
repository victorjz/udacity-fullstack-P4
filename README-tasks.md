# Udacity fullstack nanodegree Project 4
January 2016

Prepared by Victor Zaragoza vjzara@gmail.com

## Corrections since last submission
* `README.txt` is replaced with this file to make use of github markdown
* Description in `cron.yaml` updated to reflect change in time period
* Additional statement for Task 2.
* The method for generating a key for `Speaker` now uses an allocated id instead of the speaker name.  Affected code revised throughout.  Statements in this file are also revised.
* `Session` and `SessionForm` no longer contain a `conferenceKey`. Dependent code revised.
* `Session` now contains a `KeyProperty` for a `Speaker` key and Speaker now contains a repeated `KeyProperty` for `Session` at which they will speak. This changes the methods `createSessionObject`, `_copySessionToForm`, and more.
* Eliminated unnecessary lines from method `_cacheFeaturedSpeaker`
* The endpoint method `getFeaturedSpeaker` is now defined
* Repositioned put operation for better code flow in `_updateSessionWishlist`
* Projection used for `getConferenceSessionTypes`
* `_cacheFeaturedSpeaker` changed to accept just session web key instead of speaker and conference. Session contains both of these items.

## Project Notes and Setup
These files contain course provided code that implements a fictional website called Conference Central. The purpose of the project is to write additional code that adds functionality as described here in [Project details][1]

The app is deployed [here][2]

The api explorer is located [here][3]

Instructions for setting up the project via original source code are in `README.md`.  Project ID and client ID are already populated in code.

## Written Responses
### Task 1
#### Explain design choices for session and speaker implementation
Following the pattern between Conference and ConferenceForm, Session and SessionForm were created.  It could be determined from examining Conference and ConferenceFrom that the former extends Model and is necessary for storing data in the datastore, while ConferenceForm extends Messages and is necessary for passing information back to the frontend of the application. Likewise, then, were Session and SessionForm designed.


The decision made for a session speaker is to make it its own entity rather than a property.  If kept as a string property, then the query in Task 1 to search for all session entries by speaker can be problematic if sessions were ever to reach a number in the thousands or more. There is a similar problem in Task 4 when determining if there are more than one session by the same speaker in the same conference.  Keeping a speaker as an entity and recording the sessions associated with that speaker means there are likely far fewer items to examine in a query.  Also, if speaker is ever further defined, for example to record professional affiliations, ratings, etc., then as its own entity such properties can be added later.


### Task 2
Decision made to allow a user to add sessions to their wishlist _without_ registering for the conference so that a user can save the sessions they are interested in for multiple conferences and commit to attend once they gauge their interest.

All methods defined.

### Task 3
##### Make up two queries and define them

The first made up query is `getConferenceSessionsInTime`.  This is designed so that a user can enter select on a form a conference and the earliest and latest time that they are interested in starting a conference.  The query returns all sessions in the conference that start in between the times provided.  This is useful for users that have a window of available time and want to focus on the sessions they can attend during that window.  This was achieved by translating the strings representing earliest and latest time and converting those into time objects to compare with session start times.

The second made up query is `getConferenceSessionTypes`.  Given a conference, this query returns a list of the unique values of `typeOfSession`.  A front end developer can take the result of this query and present a list of links.  Those links can call the query `getConferenceSessionsByType`.  This would be helpful for users that are interested to see the types of sessions available and then drill down to the exact sessions for every type according to their interest.  For example, a user can quickly see that a conference has only lectures, panels, and interviews and decide their most interested in panels.

##### Describe the problem with the query "sessions that are not workshops starting before 7pm"

The problem is the use of inequality operators.  Inequality operators may apply at most to one property in a given query and the proposed query would require "not-equals workshop" on `typeOfSession` and "less than 7pm" on `startTime`. A possible way to solve this is to apply an inequality on one property or the other, and then filter the results in Python code.  Arbitrarily choosing to filter for non-workshops, code can be written to iterate over the query results and remove sessions that do not take place before 7pm.

Alternatively, two separate queries can be executed, one that uses an inequality operator on `typeOfSession` and the other on `startTime`, and then the results can be compared for matches, effectively producing an intersection of the results. The concern with this method is that the course learning material cautions that queries can be slow, and so this might be the less desirable of the two options. The code for the first option is in endpoints method called `noUnwantedSessionsAndBeforeTime` This is available to execute in [api explorer][3].

### Task 4
Required methods defined

[1]:https://docs.google.com/document/d/1H9anIDV4QCPttiQEwpGe6MnMBx92XCOlz0B4ciD7lOs/pub#h.ji5u5m1quiwt
[2]:https://valid-progress-115922.appspot.com/#/
[3]:https://valid-progress-115922.appspot.com/_ah/api/explorer
