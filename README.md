# utl-creating-big-N-headers-in-your-reports-corresp-clinical-ods
Creating big N headers in your reports corresp clinical ods 
    %let pgm=utl-creating-big-N-headers-in-your-reports-corresp-clinical-ods;

    Creating big N headers in your reports corresp clinical ods

    github
    https://tinyurl.com/ycxd788z
    https://github.com/rogerjdeangelis/utl-creating-big-N-headers-in-your-reports-corresp-clinical-ods

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    data class;
      set sashelp.class(keep=sex age);
      if sex="F"  then gender = "Female";
      else gender="Male";
      drop sex;
    run;quit;

    /**************************************************************************************************************************/ /
    /*                                                                                                                        */
    /* AGE    GENDER                                                                                                          */
    /*                                                                                                                        */
    /*  14    Male                                                                                                            */
    /*  13    Female                                                                                                          */
    /*  13    Female                                                                                                          */
    /*  14    Female                                                                                                          */
    /*  14    Male                                                                                                            */
    /*  12    Male                                                                                                            */
    /*  12    Female                                                                                                          */
    /*  15    Female                                                                                                          */
    /*  13    Male                                                                                                            */
    /*  12    Male                                                                                                            */
    /*  11    Female                                                                                                          */
    /*  14    Female                                                                                                          */
    /*  12    Female                                                                                                          */
    /*  15    Female                                                                                                          */
    /*  16    Male                                                                                                            */
    /*  12    Male                                                                                                            */
    /*  15    Male                                                                                                            */
    /*  11    Male                                                                                                            */
    /*  15    Male                                                                                                            */
    /*                                                                                                                        */
    /**************************************************************************************************************************/ /

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    /*----  macro variables for proc report headers                          ----*/

    proc sql;
       select
          resolve(catx(
          ' ','%let',gender,'=%str(',gender,' ^{newline}N =',
          put(count(*),2. -l),');'
       ))
       from
          class
       group
          by gender
    ;quit;

    %put &=male;
    %put &=female;

    /*----                                                                   ----*/
    /*---- MALE  = Male ^{newline}N = 10                                     ----*/
    /*---- FEMALE= Female ^{newline}N = 9                                    ----*/
    /*----                                                                   ----*/
    /*----                                                                   ----*/

    /*----  create crosstab dataset                                          ----*/
    ods output observed=counts(drop=sum where=(label ne "Sum"));
    proc corresp data=class dim=1 observed cross=both;
    tables age, gender;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Obs    LABEL    FEMALE    MALE                                                                                         */
    /*                                                                                                                        */
    /*  1      11         1        1                                                                                          */
    /*  2      12         2        3                                                                                          */
    /*  3      13         2        1                                                                                          */
    /*  4      14         2        2                                                                                          */
    /*  5      15         2        2                                                                                          */
    /*  6      16         0        1                                                                                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    ods escapechar="^";
    ods pdf file="d:/pdf/bign.pdf";
    proc report data=counts split='#';
       define label / display "Age#Group" width=8;
       define female /display "&female" width=12;
       define male /display "&male" width=12;
    run;quit;
    ods pdf close;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*    d:/pdf/bign.pdf                                                                                                     */
    /*                                                                                                                        */
    /*   Age     Female  Male                                                                                                 */
    /*   Group   N = 9  N = 10                                                                                                */
    /*                                                                                                                        */
    /*   11          1      1                                                                                                 */
    /*   12          2      3                                                                                                 */
    /*   13          2      1                                                                                                 */
    /*   14          2      2                                                                                                 */
    /*   15          2      2                                                                                                 */
    /*   16          0      1                                                                                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
