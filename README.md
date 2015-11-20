# Simple Conference Central
Simple conference central demo using Google Cloud Endpoints

## Language
- [Python 2.7] (http://python.org)

## APIs
- [Google Cloud Endpoints](https://developers.google.com/appengine/docs/python/endpoints/)

## Demo
- Check [Live Version](https://udacity-conference-1131.appspot.com/)
- Check [API Explorer](https://udacity-conference-1131.appspot.com/_ah/api/explorer)

## Tasks' Questions
####Task 1: Add Sessions to conference
The session model is pretty similar to conference model. The only difference is that every session is referencing one conference as parent.
The speaker is simply a string property in the session model. For the future improvement, the speaker will be user id and can only be selected from registered users.

####Task 3.1: Two additional queries
1. Get all types of sessions
```python
@endpoints.method(message_types.VoidMessage, SessionTypes,
                  path='sessions/types',
                  http_method='GET',
                  name='getSessionsTypes')
def getSessionsTypes(self, request):
    """GET: Get all types of session"""
    types = Session.query(projection=[Session.typeOfSession], distinct=True)
    return SessionTypes(types=[t.typeOfSession for t in types])
```
2. Get Sessions that is not given type and before given time
```python
@endpoints.method(SESSION_GET_BEFORE_TIME_REQUEST, SessionForms,
                  path='sessions/notTypebeforeTime',
                  http_method='GET',
                  name='getSessionsNotTypeBeforeTime')
def getSessionsNotTypeBeforeTime(self, request):
    """GET: Get all sessions that is not given type before time"""
    types = Session.query(projection=[Session.typeOfSession], distinct=True)
    allowtypes = [t.typeOfSession for t in types if t.typeOfSession != request.notType]
    sessions = Session.query(Session.typeOfSession.IN(allowtypes))
    startTime = datetime.strptime(request.startTime[:5], "%H:%M").time()
    sessions = sessions.filter(Session.startTime < startTime)
    return SessionForms(items=[self._copySessionToForm(session) for session in sessions])
```
####Task 3.2: Query Problem
Q: Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non-workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?

A: The solution is presented at Task 3.1 #2. Since inequality filter can not be applied to multiple properties. We first generate a list of types that excludes the given type. Use the collection and IN operator to get all the sessions that is not the given type. Then, we use inequality filter to find sessions before given time.
