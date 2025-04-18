# utl-identify-sequential-maximums-in-a-series-of-numbers-using-base-sas-and-sql-r-python-excel
Identify sequential maximums in a series of numbers using base r sas and sql r python excel
    %let pgm=utl-identify-sequential-maximums-in-a-series-of-numbers-using-base-sas-and-sql-r-python-excel;

    %stop_submission;

    Identify sequential maximums in a series of numbers using base r sas and sql r python excel

               SOLUTIONS

                   1 base r
                   2 base sas
                   3 r sql
                   4 same sql code for python and excel
                     see https://tinyurl.com/5jj9t8wu

    github
    https://tinyurl.com/hptzx3hf
    https://github.com/rogerjdeangelis/utl-identify-sequential-maximums-in-a-series-of-numbers-using-base-sas-and-sql-r-python-excel

    stackoverflow r
    https://tinyurl.com/ycxzyncs
    https://stackoverflow.com/questions/79569719/how-to-create-a-new-column-that-indicates-moving-maximum

    template for python and excel
    https://tinyurl.com/5jj9t8wu
    https://github.com/rogerjdeangelis/utl-roll-up-adverse-events-by-patient-and-date-using-sql-groupcat-r-python-and-excel

    Note: I am preserving missings.
    Also note a secong value wqual to a previous maximum is flagged as a maximum

    /**************************************************************************************************************************/
    /*                        |                                  |                                                            */
    /*    INPUT               |        PROCESS                   |      OUTPUT                                                */
    /*    =====               |        =======                   |      ======                                                */
    /*                        |                                  |                                                            */
    /*                        |             CUM  FLG             |                                                            */
    /* SEQ    VAL             |  SEQ VAL    MAX  MAX             |  SEQ    VAL  FLGMAX                                        */
    /*                        |                                  |                                                            */
    /*   1      4             |    1   4     4*   1              |    1      4     1                                          */
    /*   2      1             |    2   1     4    0              |    2      1     0                                          */
    /*   3      .             |    3   .     4    . keep missing |    3      .     .                                          */
    /*   4      3             |    4   3     4    0              |    4      3     0                                          */
    /*   5      5             |    5   5     5*   1              |    5      5     1                                          */
    /*   6      9             |    6   9     9*   1              |    6      9     1                                          */
    /*   7      6             |    7   6     9    0              |    7      6     0                                          */
    /*   8      7             |    8   7     9    0              |    8      7     0                                          */
    /*   9      8             |    9   8     9    0              |    9      8     0                                          */
    /*  10     15             |   10  15    15*   1              |   10     15     1                                          */
    /*  11     11             |   11  11    15    0              |   11     11     0                                          */
    /*  12      .             |   12   .    15    . keep missing |   12      .     .                                          */
    /*  13     13             |   13  13    15    0              |   13     13     0                                          */
    /*  14     14             |   14  14    15    0              |   14     14     0                                          */
    /*  15     15             |   15  15    15*   1              |   15     15     1                                          */
    /*  16     16             |   16  16    16*   1              |   16     16     1                                          */
    /*  17     17             |   17  17    17*   1              |   17     17     1                                          */
    /*  18     19             |   18  19    19*   1              |   18     19     1                                          */
    /*  19     18             |   19  18    19    0              |   19     18     0                                          */
    /*  20     20             |   20  20    20*   1              |   20     20     1                                          */
    /*                        |                                  |                                                            */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                  |                                                            */
    /* options                | 1 BASE R                         |  > want                                                    */
    /* validvarname=upcase;   | ========                         |     SEQ VAL want                                           */
    /* libname sd1 "d:/sd1";  |                                  |  1    1   4    1                                           */
    /* data sd1.have;         | proc datasets lib=sd1            |  2    2   1    0                                           */
    /* input seq val;         | nolist nodetails;                |  3    3  NA   NA                                           */
    /* cards4;                |  delete want;                    |  4    4   3    0                                           */
    /* 01 04                  | run;quit;                        |  5    5   5    1                                           */
    /* 02 01                  |                                  |  6    6   9    1                                           */
    /* 03 .                   | %utl_rbeginx;                    |  7    7   6    0                                           */
    /* 04 03                  | parmcards4;                      |  8    8   7    0                                           */
    /* 05 05                  | library(haven)                   |  9    9   8    0                                           */
    /* 06 09                  | library(sqldf)                   |  10  10  15    1                                           */
    /* 07 06                  | source("c:/oto/fn_tosas9x.R")    |  11  11  11    0                                           */
    /* 08 07                  | have<-read_sas(                  |  12  12  NA   NA                                           */
    /* 09 08                  |  "d:/sd1/have.sas7bdat")         |  13  13  13    0                                           */
    /* 10 15                  | x=have$VAL                       |  14  14  14    0                                           */
    /* 11 11                  | want<-(x==cummax(                |  15  15  15    1                                           */
    /* 12  .                  |   ifelse(                        |  16  16  16    1                                           */
    /* 13 13                  |      !is.na(x)                   |  17  17  17    1                                           */
    /* 14 14                  |     ,x                           |  18  18  19    1                                           */
    /* 15 15                  |     ,min(x,na.rm=T)))) + 0       |  19  19  18    0                                           */
    /* 16 16                  | want<-cbind(have,want)           |  20  20  20    1                                           */
    /* 17 17                  | want                             |                                                            */
    /* 18 19                  | fn_tosas9x(                      |                                                            */
    /* 19 18                  |       inp    = want              |                                                            */
    /* 20 20                  |      ,outlib ="d:/sd1/"          |                                                            */
    /* ;;;;                   |      ,outdsn ="want"             |                                                            */
    /* run;quit;              |      )                           |                                                            */
    /*                        | ;;;;                             |                                                            */
    /*                        | %utl_rendx;                      |                                                            */
    /*                        |                                  |                                                            */
    /*                        | proc print data=sd1.want;        |                                                            */
    /*                        | run;quit;                        |                                                            */
    /*                        |                                  |                                                            */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                  |                                                            */
    /*                        | 2 BASE SAS                       |  SEQ VAL  VALMAX  FLGMAX                                   */
    /*                        | ==========                       |                                                            */
    /*                        |                                  |    1   4     4       1                                     */
    /*                        | data want;                       |    2   1     4       0                                     */
    /*                        |   retain valMax                  |    3   .     4       .                                     */
    /*                        |          flgmax 0;               |    4   3     4       0                                     */
    /*                        |   set sd1.have;                  |    5   5     5       1                                     */
    /*                        |   if seq=1 then valMax=val;      |    6   9     9       1                                     */
    /*                        |   valMax=ifn(                    |    7   6     9       0                                     */
    /*                        |       val>valMax                 |    8   7     9       0                                     */
    /*                        |      ,val                        |    9   8     9       0                                     */
    /*                        |      ,valMax)                    |   10  15    15       1                                     */
    /*                        |   flgMax=ifn(                    |   11  11    15       0                                     */
    /*                        |      missing(val)                |   12   .    15       .                                     */
    /*                        |      ,.                          |   13  13    15       0                                     */
    /*                        |      ,(valMax=Val));             |   14  14    15       0                                     */
    /*                        | run;quit;                        |   15  15    15       1                                     */
    /*                        |                                  |   16  16    16       1                                     */
    /*                        |                                  |   17  17    17       1                                     */
    /*                        |                                  |   18  19    19       1                                     */
    /*                        |                                  |   19  18    19       0                                     */
    /*                        |                                  |   20  20    20       1                                     */
    /*                        |                                  |                                                            */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                  |                                                            */
    /*                        | 3 R SQL                          | > want                                                     */
    /*                        | =======                          |    SEQ VAL flgmax                                          */
    /*                        |                                  | 1    1   4      1                                          */
    /*                        | proc datasets lib=sd1            | 2    2   1      0                                          */
    /*                        | nolist nodetails;                | 3    3  NA     NA                                          */
    /*                        |  delete want;                    | 4    4   3      0                                          */
    /*                        | run;quit;                        | 5    5   5      1                                          */
    /*                        |                                  | 6    6   9      1                                          */
    /*                        | %utl_rbeginx;                    | 7    7   6      0                                          */
    /*                        | parmcards4;                      | 8    8   7      0                                          */
    /*                        | library(haven)                   | 9    9   8      0                                          */
    /*                        | library(sqldf)                   | 10  10  15      1                                          */
    /*                        | source("c:/oto/fn_tosas9x.R")    | 11  11  11      0                                          */
    /*                        | have<-read_sas(                  | 12  12  NA     NA                                          */
    /*                        |  "d:/sd1/have.sas7bdat")         | 13  13  13      0                                          */
    /*                        | print(have)                      | 14  14  14      0                                          */
    /*                        | want<-sqldf('                    | 15  15  15      1                                          */
    /*                        |  select                          | 16  16  16      1                                          */
    /*                        |     seq                          | 17  17  17      1                                          */
    /*                        |    ,val                          | 18  18  19      1                                          */
    /*                        |    ,((max(val)                   | 19  19  18      0                                          */
    /*                        |      over (order by seq)) = val) | 20  20  20      1                                          */
    /*                        |     as flgmax                    |                                                            */
    /*                        |  from have                       |                                                            */
    /*                        |  ')                              |                                                            */
    /*                        | want                             |                                                            */
    /*                        | fn_tosas9x(                      |                                                            */
    /*                        |       inp    = want              |                                                            */
    /*                        |      ,outlib ="d:/sd1/"          |                                                            */
    /*                        |      ,outdsn ="want"             |                                                            */
    /*                        |      )                           |                                                            */
    /*                        | ;;;;                             |                                                            */
    /*                        | %utl_rendx;                      |                                                            */
    /*                        |                                  |                                                            */
    /*                        | proc print data=sd1.want;        |                                                            */
    /*                        | run;quit;                        |                                                            */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options
    validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    input seq val;
    cards4;
    01 04
    02 01
    03 .
    04 03
    05 05
    06 09
    07 06
    08 07
    09 08
    10 15
    11 11
    12  .
    13 13
    14 14
    15 15
    16 16
    17 17
    18 19
    19 18
    20 20
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /* SEQ    VAL                                                                                                             */
    /*                                                                                                                        */
    /*   1      4                                                                                                             */
    /*   2      1                                                                                                             */
    /*   3      .                                                                                                             */
    /*   4      3                                                                                                             */
    /*   5      5                                                                                                             */
    /*   6      9                                                                                                             */
    /*   7      6                                                                                                             */
    /*   8      7                                                                                                             */
    /*   9      8                                                                                                             */
    /*  10     15                                                                                                             */
    /*  11     11                                                                                                             */
    /*  12      .                                                                                                             */
    /*  13     13                                                                                                             */
    /*  14     14                                                                                                             */
    /*  15     15                                                                                                             */
    /*  16     16                                                                                                             */
    /*  17     17                                                                                                             */
    /*  18     19                                                                                                             */
    /*  19     18                                                                                                             */
    /*  20     20                                                                                                             */
    /**************************************************************************************************************************/

    /*   _
    / | | |__   __ _ ___  ___   _ __
    | | | `_ \ / _` / __|/ _ \ | `__|
    | | | |_) | (_| \__ \  __/ | |
    |_| |_.__/ \__,_|___/\___| |_|

    */

    proc datasets lib=sd1
    nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas(
     "d:/sd1/have.sas7bdat")
    x=have$VAL
    want<-(x==cummax(
      ifelse(
         !is.na(x)
        ,x
        ,min(x,na.rm=T)))) + 0
    want<-cbind(have,want)
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /* R               SAS                                                                                                    */
    /* SEQ VAL want  |   SEQ    VAL    WANT                                                                                   */
    /*               |                                                                                                        */
    /*   1   4    1  |     1      4      1                                                                                    */
    /*   2   1    0  |     2      1      0                                                                                    */
    /*   3  NA   NA  |     3      .      .                                                                                    */
    /*   4   3    0  |     4      3      0                                                                                    */
    /*   5   5    1  |     5      5      1                                                                                    */
    /*   6   9    1  |     6      9      1                                                                                    */
    /*   7   6    0  |     7      6      0                                                                                    */
    /*   8   7    0  |     8      7      0                                                                                    */
    /*   9   8    0  |     9      8      0                                                                                    */
    /*  10  15    1  |    10     15      1                                                                                    */
    /*  11  11    0  |    11     11      0                                                                                    */
    /*  12  NA   NA  |    12      .      .                                                                                    */
    /*  13  13    0  |    13     13      0                                                                                    */
    /*  14  14    0  |    14     14      0                                                                                    */
    /*  15  15    1  |    15     15      1                                                                                    */
    /*  16  16    1  |    16     16      1                                                                                    */
    /*  17  17    1  |    17     17      1                                                                                    */
    /*  18  19    1  |    18     19      1                                                                                    */
    /*  19  18    0  |    19     18      0                                                                                    */
    /*  20  20    1  |    20     20      1                                                                                    */
    /**************************************************************************************************************************/

    /*___    _
    |___ \  | |__   __ _ ___  ___   ___  __ _ ___
      __) | | `_ \ / _` / __|/ _ \ / __|/ _` / __|
     / __/  | |_) | (_| \__ \  __/ \__ \ (_| \__ \
    |_____| |_.__/ \__,_|___/\___| |___/\__,_|___/

    */

     data want;
       retain valMax
              flgmax 0;
       set sd1.have;
       if seq=1 then valMax=val;
       valMax=ifn(
           val>valMax
          ,val
          ,valMax)
       flgMax=ifn(
          missing(val)
          ,.
          ,(valMax=Val));
     run;quit;

    /**************************************************************************************************************************/
    /* SEQ VAL  VALMAX  FLGMAX                                                                                                */
    /*                                                                                                                        */
    /*   1   4     4       1                                                                                                  */
    /*   2   1     4       0                                                                                                  */
    /*   3   .     4       .                                                                                                  */
    /*   4   3     4       0                                                                                                  */
    /*   5   5     5       1                                                                                                  */
    /*   6   9     9       1                                                                                                  */
    /*   7   6     9       0                                                                                                  */
    /*   8   7     9       0                                                                                                  */
    /*   9   8     9       0                                                                                                  */
    /*  10  15    15       1                                                                                                  */
    /*  11  11    15       0                                                                                                  */
    /*  12   .    15       .                                                                                                  */
    /*  13  13    15       0                                                                                                  */
    /*  14  14    15       0                                                                                                  */
    /*  15  15    15       1                                                                                                  */
    /*  16  16    16       1                                                                                                  */
    /*  17  17    17       1                                                                                                  */
    /*  18  19    19       1                                                                                                  */
    /*  19  18    19       0                                                                                                  */
    /*  20  20    20       1                                                                                                  */
    /**************************************************************************************************************************/

    /*____                    _
    |___ /   _ __   ___  __ _| |
      |_ \  | `__| / __|/ _` | |
     ___) | | |    \__ \ (_| | |
    |____/  |_|    |___/\__, |_|
                           |_|
    */

    proc datasets lib=sd1
    nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas(
     "d:/sd1/have.sas7bdat")
    print(have)
    want<-sqldf('
     select
        seq
       ,val
       ,((max(val)
         over (order by seq)) = val)
        as flgmax
     from have
     ')
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*  R               |   SAS                                                                                               */
    /*  SEQ VAL FLGMAX  |   ROWNAMES    SEQ    VAL    FLGMAX                                                                  */
    /*                  |                                                                                                     */
    /*    1   4      1  |       1         1      4       1                                                                    */
    /*    2   1      0  |       2         2      1       0                                                                    */
    /*    3  NA     NA  |       3         3      .       .                                                                    */
    /*    4   3      0  |       4         4      3       0                                                                    */
    /*    5   5      1  |       5         5      5       1                                                                    */
    /*    6   9      1  |       6         6      9       1                                                                    */
    /*    7   6      0  |       7         7      6       0                                                                    */
    /*    8   7      0  |       8         8      7       0                                                                    */
    /*    9   8      0  |       9         9      8       0                                                                    */
    /*   10  15      1  |      10        10     15       1                                                                    */
    /*   11  11      0  |      11        11     11       0                                                                    */
    /*   12  NA     NA  |      12        12      .       .                                                                    */
    /*   13  13      0  |      13        13     13       0                                                                    */
    /*   14  14      0  |      14        14     14       0                                                                    */
    /*   15  15      1  |      15        15     15       1                                                                    */
    /*   16  16      1  |      16        16     16       1                                                                    */
    /*   17  17      1  |      17        17     17       1                                                                    */
    /*   18  19      1  |      18        18     19       1                                                                    */
    /*   19  18      0  |      19        19     18       0                                                                    */
    /*   20  20      1  |      20        20     20       1                                                                    */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
