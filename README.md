# CBT826
Converted to GitHub via [cbt2git](https://github.com/wizardofzos/cbt2git)

This is still a work in progress. 
Due to amazing work by Alison Zhang and Jake Choi repos are no longer deleted.

```
//***FILE 826 is from Sam Golob and contains a FREE TSO command to  *   FILE 826
//*           alter the status of an address space, by making it    *   FILE 826
//*           Cancelable or Non-Cancelable, Non-Memtermable (can't  *   FILE 826
//*           be FORCEd), Memtermable, Swappable, Non-Swappable.    *   FILE 826
//*           (Also if FORCE needs a CANCEL).  Joe Reichman put     *   FILE 826
//*           in Swappable and Non-Swappable for any address space. *   FILE 826
//*           This command is one of the very rare FREE commands    *   FILE 826
//*           (APF-authorized) which can make any address space     *   FILE 826
//*           non-swappable or swappable.  Thanks, Joe!             *   FILE 826
//*                                                                 *   FILE 826
//*           The current version of CNCLPG is 1.2.10F .            *   FILE 826
//*           Some previous versions are also included here.        *   FILE 826
//*                                                                 *   FILE 826
//*           The current version can TRANSWAP any address space,   *   FILE 826
//*           (function TSWP) and can display the count of noswap   *   FILE 826
//*           requests (function COUNT--and others).  You have to   *   FILE 826
//*           be very careful to use TRANSWAP only when necessary.  *   FILE 826
//*                                                                 *   FILE 826
//*      ->   Additionally, there is now included a program called  *   FILE 826
//*      ->   WAITT which can alter the job step wait time of your  *   FILE 826
//*      ->   TSO session, or turn on the exemption bit to allow    *   FILE 826
//*      ->   disregard of the wait time altogether.  (Can turn     *   FILE 826
//*      ->   it off, too.)  See members WAITT*                     *   FILE 826
//*                                                                 *   FILE 826
//*      ->   Addition of WAITT to this file, gives you the         *   FILE 826
//*      ->   enhanced ability to control your session's            *   FILE 826
//*      ->   wait time limit, which CNCLPG doesn't currently do.   *   FILE 826
//*                                                                 *   FILE 826
//*  <<****>> Please read the #CNCLPG# member of this pds before    *   FILE 826
//*  <<****>> using the CNCLPG command.  It is an "easy read".      *   FILE 826
//*                                                                 *   FILE 826
//*  <<****>> Also please read member $DESCR, which is important,   *   FILE 826
//*  <<****>> if you want to use this command SAFELY....!!!         *   FILE 826
//*                                                                 *   FILE 826
//*           Non-Forcible here, is:  *NOT* "NON-FORCIBLE" in the   *   FILE 826
//*           absolute sense of "NON-MEMTERMABLE".  Rather, it is   *   FILE 826
//*           the bit in the CSCB which requires that you have to   *   FILE 826
//*           do a CANCEL before you do a FORCE.                    *   FILE 826
//*                                                                 *   FILE 826
//*           If you want to make an address space really           *   FILE 826
//*           "NON-MEMTERMABLE", then use action NMEM in CNCLPG.    *   FILE 826
//*           "MEMTERMABLE" (or really Forcible) is action MEMT.    *   FILE 826
//*                                                                 *   FILE 826
//*           To display how many "DONTSWAP" requests exist         *   FILE 826
//*           against an address space, use the function COUNT.     *   FILE 826
//*                                                                 *   FILE 826
//*           Sometimes there are multiple DONTSWAP actions         *   FILE 826
//*           against an address space.  To turn them all off,      *   FILE 826
//*           and make the address space SWAPPABLE, use FSWP        *   FILE 826
//*           (or Forced SWAP).  Otherwise run the SWAP action      *   FILE 826
//*           multiple times (FSWP eliminates that problem).        *   FILE 826
//*           But use FSWP very carefully, because there might      *   FILE 826
//*           be multiple reasons why the address space is          *   FILE 826
//*           currently nonswappable.  This is only an emergency    *   FILE 826
//*           tool.                                                 *   FILE 826
//*                                                                 *   FILE 826
//*           To use the CNCLPG TSO command, you first find the     *   FILE 826
//*           correct address space, and then you do an operation   *   FILE 826
//*           on it.  (See the use of the DISP action, described    *   FILE 826
//*           below.  THIS IS VERY IMPORTANT....!!!)                *   FILE 826
//*                                                                 *   FILE 826
//*     Syntax:     TSO Command:  APF-authorized.  (v. 1.2.10F)     *   FILE 826
//*                                                                 *   FILE 826
//*  CNCLPG |jobid ÝASID(hhhh)¨ |CNCL|NCNC (cancelable,             *   FILE 826
//*         |                   |           non-cancelable)         *   FILE 826
//*         |ASID(hhhh)         |FORC    (force w/o cancel)         *   FILE 826
//*         |                   |NFRC    (force needs cancel first) *   FILE 826
//*         |                   |NSWP|SWAP (non-swap, swappable)    *   FILE 826
//*         |                   |TSWP (transwap)                    *   FILE 826
//*         |                   |FSWP (force swappable immediately) *   FILE 826
//*         |                   |BURN (memterm the address space)   *   FILE 826
//*         |                   |KILL (do memterm after turning     *   FILE 826
//*         |                   |      off non-memtermable bits)    *   FILE 826
//*         |                   |NMEM|MEMT  (turn on/off            *   FILE 826
//*         |                   |        non-memtermable bits)      *   FILE 826
//*         |                   |CSCB or DISP (display CSCB blks)   *   FILE 826
//*         |                   |COUNT  (display DONTSWAP count)    *   FILE 826
//*         |-  (displays the entire CSCB chain)                    *   FILE 826
//*         |?  (displays help)                                     *   FILE 826
//*                                                                 *   FILE 826
//*           !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!   *   FILE 826
//*                                                                 *   FILE 826
//*           The following also applies to higher releases of      *   FILE 826
//*           the CNCLPG program:  (current V1R210E)                *   FILE 826
//*                                                                 *   FILE 826
//*           V1R2M8 of the CNCLPG program also includes a BURN     *   FILE 826
//*           parameter to remove the address space if necessary.   *   FILE 826
//*           Also NMEM (make Non-MEMTERMABLE) and MEMT (make       *   FILE 826
//*           Memtermable) added in V1R2M8.                         *   FILE 826
//*                                                                 *   FILE 826
//*           As coded, you need PSCB ACCOUNT privilege to run      *   FILE 826
//*           the BURN, KILL, NMEM actions.  You need PSCB OPER     *   FILE 826
//*           privilege to run the program.  You may add code       *   FILE 826
//*           to secure this program better, to your needs.         *   FILE 826
//*                                                                 *   FILE 826
//*              THIS IS FOR EMERGENCY USE ONLY. IF THE BURN        *   FILE 826
//*              FACILITY IS NOT WANTED, INSTALL CNCLPG20.          *   FILE 826
//*                                                                 *   FILE 826
//*    NOTE:  !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!   *   FILE 826
//*    -----                                                        *   FILE 826
//*    >>>>>  If you think BURN or KILL are too dangerous, then     *   FILE 826
//*    >>>>>  you can install CNCLPG20 instead, which is a          *   FILE 826
//*    >>>>>  previous version that cannot do BURN or KILL.         *   FILE 826
//*                                                                 *   FILE 826
//*           !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!   *   FILE 826
//*                                                                 *   FILE 826
//*           V1R2M8 of CNCLPG includes the KILL option, which      *   FILE 826
//*           you can think of as "BURN plus".  KILL turns off      *   FILE 826
//*           the no-MEMTERM bits in the ASID before issuing a      *   FILE 826
//*           CALLRTM MEMTERM.  Mitigating factor is that you       *   FILE 826
//*           need PSCB OPER authority to run this version of       *   FILE 826
//*           CNCLPG.  And to do BURN or KILL, you ALSO need        *   FILE 826
//*           PSCB ACCOUNT authority, as well as OPER authority.    *   FILE 826
//*                                                                 *   FILE 826
//*           If you install this command, you are welcome to add   *   FILE 826
//*           additional authorization checks to it, obviously.     *   FILE 826
//*                                                                 *   FILE 826
//*           !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!   *   FILE 826
//*                                                                 *   FILE 826
//*           You can TRANSWAP a swappable address space, using     *   FILE 826
//*           the TSWP function of CNCLPG.                          *   FILE 826
//*                                                                 *   FILE 826
//*           This command also contains fairly detailed messaging  *   FILE 826
//*           and displays, as well as in-context help:  CNCLPG ?   *   FILE 826
//*                                                                 *   FILE 826
//*           You can run and display the entire CSCB chain by      *   FILE 826
//*           typing a dash ( - ) instead of the jobname.           *   FILE 826
//*                                                                 *   FILE 826
//*           You can display ALL eligible CSCB entries that        *   FILE 826
//*           satisfy a jobname equal, or and ASID(hhhh) equal,     *   FILE 826
//*           by using the DISP or CSCB keyword parameter after     *   FILE 826
//*           specifying either jobname, ASID(hhhh), or both.       *   FILE 826
//*                                                                 *   FILE 826
//*           If you issue a command to CHANGE an address space:    *   FILE 826
//*           CNCL, NCNC, FORC, NFRC, NSWP or SWAP - then the       *   FILE 826
//*           FIRST CSCB entry encountered, is the one changed.     *   FILE 826
//*           ----- ---- ----- -----------         --- -------      *   FILE 826
//*                                                                 *   FILE 826
//*           Therefore it pays to issue a DISP or CSCB (display)   *   FILE 826
//*           command first, to find ALL eligible entries for the   *   FILE 826
//*           address space in the CSCB chain.  Then issue the      *   FILE 826
//*           "action" command to change the exact address space    *   FILE 826
//*           that you want to change, specifying BOTH jobname      *   FILE 826
//*           and ASID(hhhh).                                       *   FILE 826
//*                                                                 *   FILE 826
//*           Joe Reichman put in the SWAP and NSWP service for     *   FILE 826
//*           different target address spaces than the one that     *   FILE 826
//*           issued the CNCLPG command.  He did all the cross      *   FILE 826
//*           memory stuff (scheduling the SRB) that was needed     *   FILE 826
//*           to make a different address space non-swappable       *   FILE 826
//*           or swappable.                                         *   FILE 826
//*                                                                 *   FILE 826
//*           CNCLPG is an APF-authorized TSO command.  To learn    *   FILE 826
//*           more about "personal" APF-authorization of TSO        *   FILE 826
//*           commands, see CBT Files 185 and 797.                  *   FILE 826
//*                                                                 *   FILE 826
//*           See pds member $DESCR for more details on how this    *   FILE 826
//*           command works.  It is very important to read this     *   FILE 826
//*           member, and you really shouldn't use the CNCLPG       *   FILE 826
//*           command without doing so.                             *   FILE 826
//*                                                                 *   FILE 826
//*           For more details about the history and structure of   *   FILE 826
//*           the CNCLPG program, please read member #CNCNPG#.      *   FILE 826
//*                                                                 *   FILE 826
//*           This is a FREE command, with functionality that       *   FILE 826
//*           is usually obtainable only in expensive vendor        *   FILE 826
//*           packages.  And it's here for you to enjoy.            *   FILE 826
//*                                                                 *   FILE 826
//*       email:  sbgolob@cbttape.org                               *   FILE 826
//*                                                                 *   FILE 826
```
