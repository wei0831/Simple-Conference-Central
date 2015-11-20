# Simple Conference Central
Simple conference central demo using Google Cloud Endpoints

## Language
- [Python 2.7] (http://python.org)

## APIs
- [Google Cloud Endpoints](https://developers.google.com/appengine/docs/python/endpoints/)

## Demo
- Check [Live Version](https://udacity-conference-1131.appspot.com/)
- Check [API Explorer](https://udacity-conference-1131.appspot.com/_ah/api/explorer)

## Setup Instructions
1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
1. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][4].
1. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
1. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
1. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting your local server's address (by default [localhost:8080][5].)
1. (Optional) Generate your client library(ies) with [the endpoints tool][6].
1. Deploy your application.

## To Run (Local)
1. Install and Open Google App Engine Launcher
1. Add project folder by press '+' on the bottom left
1. Select the newly added project and click 'Run'
1. Click 'Browse' to see the application
1. Click 'Logs' to see the feedback from backend
1. Click 'SDK Console' to manage datastore, memcache, and so on

## Tasks' Questions
####Task 1: Add Sessions to conference
A conference consists several sessions.
Given the requirement that session should have the following variables
- Session name
- highlights
- speaker
- duration
- typeOfSession
- date
- startTime

Session object inherits from ndb.Model. For simplicity, speaker is made with StringProperty that allows only one speaker associated with one session object. For future improvement, it should allow association with multiple speakers, and each speaker is a full fledged entity. The rest of variables are pretty straight forward. 'Highlights' is repeated StringProperty records multiple string tags. 'Duration' is IntegerProperty that records the minutes of the session.

To create a session, the conference key is required since each session object is associated with one conference as it's parent. Another requirement is that current user has to be the conference's organizer in order to create the session. Lastly, the name of the session has to present. The rest of fields will be filled with default values if not provided.

For Task#4, after creating the session object, it will check if the speaker has more than one sessions in the given conference. If so, it will create a task and push to the task queue to feature the speaker.

####Task 3.1: Two additional queries
1. Get all types of sessions
```python
@endpoints.method(message_types.VoidMessage, SessionTypes,
                  path='sessions/types',
                  http_method='GET',
                  name='getSessionsTypes')
```
User can use this endpoint to get a list of types of sessions in a array.  
eg. ['Type 1', 'Type 2', 'Type 3']


2. Get Sessions that is not given type and before given time
```python
@endpoints.method(SESSION_GET_BEFORE_TIME_REQUEST, SessionForms,
                  path='sessions/notTypebeforeTime',
                  http_method='GET',
                  name='getSessionsNotTypeBeforeTime')
```
User can use this endpoint to get list of sessions that excludes given type of session before certain time.
For future improvement, it should allow user to specific a list of types for exclusion.

####Task 3.2: Query Problem
Q: Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non-workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?

A: The solution is presented at Task 3.1 #2. Since inequality filter can not be applied to multiple properties. We first generate a list of types that excludes the given type. Use the collection and IN operator to get all the sessions that is not the given type. Then, we use inequality filter to find sessions before given time.
