# utl_logging_status_and_continuing_execution_after_errors
Logging status and Continuing execution after errors  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph.
    Logging status and Continuing execution after errors

    Problem: Check the status of a connection and table within a mySQL database and
    pause execution until the connection and table is available or iteration=5.

    github
    https://github.com/rogerjdeangelis/utl_logging_status_and_continuing_execution_after_errors

    INPUT
    =====

       Check: Connection

        libname mysqllib mysql user=root password="###########" database=sakila;

        Check: mySQL Actor table

           actor_id  first_name  last_name
           ---------------------------------
                184  HUMPHREY    GARLAND
                185  MICHAEL     BOLGER
                186  JULIA       ZELLWEGER
                187  RENEE       BALL
                188  ROCK        DUKAKIS

    PROCESS
    =======

     options noerrorabend;

     %symdel
       ErrorTextLib
       ErrorTextData
       ErrorCodeLib
       ErrorCodeData / nowarn;

     data log(keep= iter status errortextlib errortextdata errorcodelib errorcodedata);

       retain
          status iter errortextlib errortextdata errorcodelib errorcodedata;
       length
          ErrorTextLib
          ErrorTextData
          ErrorCodeLib
          ErrorCodeData
                         $96;

        do iter=1 by 1 until(condition=0);

               rc=dosubl('

                  libname mysqllib mysql user=root password="#######" database=sakila;
                  %let ErrortextLib= &syserrortext;
                  %let ErrorcodeLib= &syserr;
                  run;quit;

                  proc sql;
                      create table want as
                      select * from mysqllib.actor;
                  ;quit;

                  %let ErrortextData= &syserrortext;
                  %let ErrorcodeData= &syserr;

                  libname mysqllib clear;
               ');

               ErrorTextLib  =  symget('ErrorTextLib');
               ErrorTextData =  symget('ErrorTextData');
               ErrorCodeLib  =  symget('ErrorCodeLib');
               ErrorCodeData =  symget('ErrorCodeData');

               condition=(
                      rc ne 0
                  or ErrorCodeLib  ne "0"
                  or ErrorCodeData ne "0" );

               if condition then do;
                   Status        =  "Failed      ";
                   output;
                   if iter = 5 then do;
                      Status="Stopped due to 5 unsucessful attempts";
                      stop;
                  end;
                  call sleep(60);
               end;
               else do;
                    Status="Completed";
                    output;
                    stop;
               end;
        end;
        stop;

    run;quit;


    OUTPUT
    ======

     WORK.LOG total obs=5

        STATUS    ITER    ERRORTEXTLIB                                   ERRORTEXTDATA                  ERRORCODELIB    ERRORCODEDATA

        Failed      1     Error in the LIBNAME statement.     File MYSQLLIB.actors.DATA does not exist.     2032              1012
        Failed      2     Error in the LIBNAME statement.     File MYSQLLIB.actors.DATA does not exist.     2032              1012
        Failed      3     Error in the LIBNAME statement.     File MYSQLLIB.actors.DATA does not exist.     2032              1012
        Failed      4     Error in the LIBNAME statement.     File MYSQLLIB.actors.DATA does not exist.     2032              1012
        Completed   5                                                                                          0                 0


     WORK.ACTOR total obs=200

                       FIRST_
       ACTOR_ID    NAME         LAST_NAME

           1       PENELOPE     GUINESS
           2       NICK         WAHLBERG
           3       ED           CHASE
           4       JENNIFER     DAVIS
           5       JOHNNY       LOLLOBRIGIDA
           6       BETTE        NICHOLSON


     see
     https://communities.sas.com/t5/Base-SAS-Programming/Continue-after-errors/td-p/43386


    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;


       Check: Connection

        libname mysqllib mysql user=root password="###########" database=sakila;

        Check: mySQL Actor table

           actor_id  first_name  last_name
           ---------------------------------
                184  HUMPHREY    GARLAND
                185  MICHAEL     BOLGER
                186  JULIA       ZELLWEGER
                187  RENEE       BALL
                188  ROCK        DUKAKIS

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    options noerrorabend;

    %symdel
       ErrorTextLib
       ErrorTextData
       ErrorCodeLib
       ErrorCodeData / nowarn;

    data log(keep= iter status errortextlib errortextdata errorcodelib errorcodedata);

       retain
          status iter errortextlib errortextdata errorcodelib errorcodedata;
       length
          ErrorTextLib
          ErrorTextData
          ErrorCodeLib
          ErrorCodeData
                         $96;

        do iter=1 by 1 until(condition=0);

               rc=dosubl('

                  libname mysqllib mysql user=root password="sas28rlx" database=sakila;
                  %let ErrortextLib= &syserrortext;
                  %let ErrorcodeLib= &syserr;

                  proc sql;
                      select * from mysqllib.actor;
                  ;quit;

                  %let ErrortextData= &syserrortext;
                  %let ErrorcodeData= &syserr;

                  libname mysqllib clear;
               ');

               ErrorTextLib  =  symget('ErrorTextLib');
               ErrorTextData =  symget('ErrorTextData');
               ErrorCodeLib  =  symget('ErrorCodeLib');
               ErrorCodeData =  symget('ErrorCodeData');

               condition=(
                      rc ne 0
                  or ErrorCodeLib  ne "0"
                  or ErrorCodeData ne "0" );

               if condition then do;
                   Status        =  "Failed      ";
                   output;
                   if iter = 5 then do;
                      Status="Stopped due to 5 unsucessful attempts";
                      stop;
                  end;
                  call sleep(60);
               end;
               else do;
                    Status="Completed";
                    output;
                    stop;
               end;
        end;
        stop;

    run;quit;

    options errorabend;

