# utl-No-need-to-convert-your-datastep-code-to-macro-code-use-DOSUBL
No need to convert your datastep code to macro code use DOSUBL
    No need to convert your datastep code to macro code use DOSUBL

    github
    https://tinyurl.com/y4aatxz2
    https://github.com/rogerjdeangelis/utl-No-need-to-convert-your-datastep-code-to-macro-code-use-DOSUBL-

    The datastep id much richer in functionality than the macro language so
    use DOSUBL to execute your datastep code at macro time.

    Also consider  macros barray and bdo_over for automated datastep functionality at macro time (even datastep arrays)
                   Bartosz Jablonski
                   yabwon@gmail.com

    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories

    Stackoverflow
    https://stackoverflow.com/questions/56118805/sas-substringing-inside-of-a-macro

    I have a sample code that works in a data step (outside of a macro) but
    it doesn't work when I try to convert it to a macro.
    I have tried different types of ways to do it with CALL SYMPUTS, SYSFUNC, LETs, etc..
    The code listed below is what I'm trying to make into a macro that inputs the T_Test2.

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    data have;
      input col1 $7. ;
      oldCol1=col1;
    cards4;
    65-003
    ;;;;
    run;quit;

    Up to 40 obs WORK.HAVE total obs=1

    Obs     COL1     OLDCOL1

     1     65-003    65-003

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    WORK.WANT total obs=1

      OLDCOL1  COL1                                 RESULT

      65-003   653    Datastep code changed 65-003 to 653 during macro excecution time

     *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    * some arbitrary datastep code code to do complex string operations;

    %symdel col1 /nowarn;

    proc datasets lib=work;
      delete want;
    run;quit;

    %macro create_temp();

       %let rc=%sysfunc(dosubl('
       DATA temp;
           SET have;
           IF INPUT(SCAN(COL1, 1, "-"), best.)=0 THEN COL1=SUBSTR(COL1, INDEX(COL1,"-")+1);
           string="-00000000000000000000";
           DO I = 1 TO 20;
               COL1 = TRANWRD(COL1, trim(string), " ");
               string = substr(string, 1, length(string)-1);
               PUT string;
           END;
           COL1=COMPRESS(COL1);
           call symputx("col1",col1);
           drop STRING I;
       RUN;

    '));

    %mend create_temp;

    data want;

       %create_temp;

       set temp;

       if symget('col1')="653" then do;
           result="Datastep code changed 65-003 to 653 during macro excecution time";
           output;
       end;

    run;quit;


