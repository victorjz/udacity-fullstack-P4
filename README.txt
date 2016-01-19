January 2016
Udacity Fullstack nanodegree Project 4
Prepared by Victor Zaragoza vjzara@gmail.com

These files contain course provided code that implements a fictional website
called Conference Central. The purpose of the project is to write additional
code that adds functionality as described here.
https://docs.google.com/document/d/1H9anIDV4QCPttiQEwpGe6MnMBx92XCOlz0B4ciD7lOs/pub#h.ji5u5m1quiwt

The app is deployed here:
https://valid-progress-115922.appspot.com/#/

The api explorer is located here:
https://valid-progress-115922.appspot.com/_ah/api/explorer

To deploy using source code, uncompress the provided zip file and review the
course-provided instructions in README.md

TASK 1 WRITTEN RESPONSES --Explain design choices for session and speaker
implementation.  Following the pattern between Conference and ConferenceForm,
Session and SessionForm were created.  It could be determined from examining
Conference and ConferenceFrom that the former is extends Model and is necessary
for storing data in the datastore, while ConferenceForm extends Messages and is
necessary for passing information back to the frontend of the application.
Likewise, then, were Session and SessionForm designed.
 The differences between
Session and SessionForm follow conventions observed in the differences between
Conference an ConferenceForm. For data that refers to anything other than a
Session, then Session will strictly contain a websafekey referencing the other
class, while sessionForm might contain the name of the other class object in
addition to the websafekey. And so Session contains ConferenceKey while
SessionForm contains ConferenceKey and conferenceName.  This mirrors the fact
that Conference contains just organizerUserId while ConferenceForm contains that
item plus organizerDisplayName.  The decision made for a session speaker is to
make it its own entity rather than a property.  If kept as a string property,
then the query in Task 1 to search for all session entries by speaker can be
problematic if sessions were ever to reach a number in the thousands or more.
There is a similar problem in Task 4 when determining if there are more than one
session by the same speaker in the same conference.  Keeping a speaker as an
entity and recording the sessions associated with that speaker means there are
likely far fewer items to examine in a query.  Also, if speaker is ever further
defined, for example to record professional affiliations, ratings, an ID, etc.,
then as its own entity such properties can be added later.
  As of now, Speaker keys are based on the speaker name only, but it would be
easy enough to decide on some different data element for creating a better key
later, if it were ever decided that speakers must register and obtain a
registration ID, for example.

TASK 3 WRITTEN RESPONSES
  The first made up query is getConferenceSessionsInTime.  This is designed so
that a user can enter select on a form a conference and the earliest and latest
time that they are interested in starting a conference.  The query returns all
sessions in the conference that start in between the times provided.  This is
useful for users that have a window of available time and want to focus on the
sessions they can attend during that window.  This was achieved by translating
the strings representing earliest and latest time and converting those into time
objects to compare with session start times.
  The second made up query is getConferenceSessionTypes.  Given a conference, this
query returns a list of the unique values of typeOfSession.  A front end
developer can take the result of this query and present a list of links.  Those
links can call the query getConferenceSessionsByType.  This would be helpful for
users that are interested to see the types of sessions available and then drill
down to the exact sessions for every type according to their interest.  For
example, a user can quickly see that a conference has only lectures, panels, and
interviews and decide their most interested in panels.
  For the query "sessions that are not workshops starting before 7pm", the problem
is the use of inequality operators.  Inequality operators may apply at most to
one property in a given query and the proposed query would require "not-equals
workshop" on typeOfSession and "less than 7pm" on startTime.
  A possible way to solve this is to apply an inequality on one property or the
other, and then filter the results in Python code.  Arbitrarily choosing to
filter for non-workshops, code can be written to iterate over the query results
and remove sessions that do not take place before 7pm.
  Alternatively, two separate queries can be executed, one that uses an inequality
operator on typeOfSession and the other on startTime, and then the results can
be compared for matches, effectively producing an intersection of the results.
The concern with this method is that the course learning material cautions that
queries can be slow, and so this might be the less desirable of the two options.
  The code for the first option is in endpoints method called:
  noUnwantedSessionsAndBeforeTime
  This is available to execute in api explorer.
