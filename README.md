# Vanilla JavaScript Seed App for SASjs

This is the minimal seed app for [SASjs](https://github.com/macropeople/sasjs).

It runs on both SAS 9 and Viya.  To deploy the services, execute the following:

```
/* define the app location, eg in metadata or Viya folders service */
%let apploc=/Public/myapp;

/* include macros directly, else download & compile manually */
filename mc url "https://raw.githubusercontent.com/macropeople/macrocore/master/mc_all.sas";
%inc mc;

/* create the two services */
filename ft15f001 temp;
parmcards4;
    proc sql;
    create table areas as select distinct area from sashelp.springs;
    %webout(OPEN)
    %webout(OBJ,areas)
    %webout(CLOSE)
;;;;
%mp_createwebservice(path=&apploc/common, name=appInit)

parmcards4;
    %webout(FETCH)
    proc sql;
    create table springs as select * from sashelp.springs
      where area in (select area from areas);
    %webout(OPEN)
    %webout(OBJ,springs)
    %webout(CLOSE)
;;;;
%mp_createwebservice(path=&apploc/common, name=getData)
```

Next, clone this repo, open the `index.html` file and update the `appLoc` in the `initSasJs()` function to the same folder location used above.  Deploy to the SAS Web Server.

You are done!


## Local Development

You can put the frontend [directly on the SAS Web Server](https://sasjs.io/frontend/deployment/), else you can also use node to spin up a local web server with CORS disabled.  To install, submit `npm install http-server -g`.  To execute, navigate to the location where the app is to be loaded and submit: `npx http-server --cors`
