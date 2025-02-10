# Jeopardy SPA

## About
Jeopardy Single Page Application that utilizes [opentdb trivia](https://opentdb.com)  database API for questions and categories.
Main branch has three players with five categories. Made using VueJS. 

### Screenshots

![Screenshot1](https://github.com/knangcas/JeopardyVue/blob/main/Screenshots/ss7.png?raw=true)

![Screenshot2](https://github.com/knangcas/JeopardyVue/blob/main/Screenshots/ss5.png?raw=true)

![Screenshot3](https://github.com/knangcas/JeopardyVue/blob/main/Screenshots/ss6.png?raw=true)

##


## Double branch
User can select a dynamic number of players and categories. 
Has a chance for "double jeopardy", coded like a "Daily Double"
Max limit is around 16 because of the width. 

### Screenshots

![Screenshot1](https://github.com/knangcas/JeopardyVue/blob/main/Screenshots/ss8.png?raw=true)

![Screenshot2](https://github.com/knangcas/JeopardyVue/blob/main/Screenshots/ss4.png?raw=true)

![Screenshot3](https://github.com/knangcas/JeopardyVue/blob/main/Screenshots/ss3.png?raw=true)

![Screenshot4](https://github.com/knangcas/JeopardyVue/blob/main/Screenshots/ss2.png?raw=true)

![Screenshot5](https://github.com/knangcas/JeopardyVue/blob/main/Screenshots/ss1.png?raw=true)


## Disclaimer

This was a class project for web applications class.

Functionality depends on [opentrivia](https://opentdb.com) as questions are fetched using this API. 
API limits the calls to every 5 seconds, which is why the SPA has to "load" the questions. 
Ideally, a set of random questions would be pulled from OpenTdb and saved locally, which eliminates the 
loading, which may be considered for in a future version. One of the goals of the class assignment was to 
use promises in JS to call the API. 
