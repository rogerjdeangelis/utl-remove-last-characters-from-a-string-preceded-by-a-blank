# utl-remove-last-characters-from-a-string-preceded-by-a-blank
Remove last characters from a string preceded by a blank.

    Remove last characters from a string preceded by a blank

      Two Solutions  (no need for regular expressions)

            1. Call Scan  (best most flexible)
            2. Call utl_pop (my function)
            3. Findc(trim(cname), ' ', 'b')
               Andreas_Ids
               https://communities.sas.com/t5/user/viewprofilepage/user-id/15475

      Solution #2 uses my 'utl_pop' FCMP function on end of this message.
      The function pops off first or last blank separated word.
      It returs the popoffed word and the reduced string.


    github
    https://tinyurl.com/y9qmc9h2
    https://github.com/rogerjdeangelis/utl-remove-last-characters-from-a-string-preceded-by-a-blank

    SAS Forum
    https://tinyurl.com/yctv9f8p
    https://communities.sas.com/t5/SAS-Programming/Remove-last-characters-from-a-string-preceded-by-a-blank-company/m-p/528241


    INPUT
    =====


    data have;
      input cname $40.;
    cards4;
    A SIM  &A G SIMP A
    A SIM  INTER
    A SIM  CROMER
    A SIM  UBER
    A SIM  HAVEDONE
    A SIM  HA G SIMP PAINTED
    ;;;;
    run;

                                |
     WORK.HAVE total obs=6      |
                                |   Last word removed
       CNAME                    |   CNAME               ENDWRD
                                |
       A SIM  INTER             |   A SIM               INTER
       A SIM  &A G SIMP A       |   A SIM  &A G SIMP    A
       A SIM  CROMER            |   A SIM               CROMER
       A SIM  UBER              |   A SIM               UBER
       A SIM  HAVEDONE          |   A SIM               HAVEDONE
       A SIM  HA G SIMP PAINTED |   A SIM  HA G SIMP    PAINTED


    EXAMPLE OUTPUT
    --------------

     WORK.WANT total obs=6

       CNAME               ENDWRD

       A SIM               INTER
       A SIM  &A G SIMP    A
       A SIM               CROMER
       A SIM               UBER
       A SIM               HAVEDONE
       A SIM  HA G SIMP    PAINTED


    PROCESS
    =======

    ----------------------------------
    1. Call Scan  (best more flexible)
    ----------------------------------

    data want;
      set have;

        call scan(cname, -1, position, length, ' ');

        endWrd = substr(cname,position,length);
        cname  = substr(cname,1,position-1);

        drop position length;
    run;quit;


    ----------------------------
    2. call utl_pop (my functio)
    ----------------------------

    data want;

      retain endWrd "          ";

      set have;
      call utl_pop(cname, endWrd, "LAST");

    run;quit;


    -------------------------------
    3. Findc(trim(cname), ' ', 'b')
    --------------------------------

    data Want;
       length cname $ 40 string $ 40 endWrd $40;

       set have;

       cname = substr(cname, 1, findc(trim(cname), ' ', 'b'));
       endWrd= scan(cname,-1,' ');

    run;quit;



    OUTPUT
    ======

    see above

    * __
     / _| ___ _ __ ___  _ __
    | |_ / __| '_ ` _ \| '_ \
    |  _| (__| | | | | | |_) |
    |_|  \___|_| |_| |_| .__/
                       |_|
    ;

    * I have this in my autoexec file;

    options cmplib=work.functions;
    proc fcmp outlib=work.functions.temp;
    Subroutine utl_pop(string $,word $,action $);
        outargs word, string;
        length word $4096;
        select (upcase(action));
          when ("LAST") do;
            call scan(string,-1,_action,_length,' ');
            word=substr(string,_action,_length);
            string=substr(string,1,_action-1);
          end;

          when ("FIRST") do;
            call scan(string,1,_action,_length,' ');
            word=substr(string,_action,_length);
            string=substr(string,_action + _length);
          end;

          otherwise put "ERROR: Invalid action";

        end;
    endsub;
    run;quit;



