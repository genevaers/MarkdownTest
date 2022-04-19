# GVBMR95R DD Statements 		
  
| Type | DD Name | Title | Required? | DSORG | RECFM | LRECL |
|:-|:-|:-|:-|:-|:-|-:|
| Input | REFRPARM | GVBMR95R Parameter File | No | PS | FB | 80 |
| | REFRTPRM | Trace Parameter File | If TRACE=Y | PS | FB | 80 |
| | REFRENVV | Environment Variable File | No | PS | FB | 80 |
| | MR95VDP  | View Definition Parameters File | Yes | PS | VB | 32756 |
| | REFRLTBL | Reference-Phase Logic Table | Yes | PS | VB | 32756 |
| | (User-Defined) | Reference Data Files | A file is required for every lookup table used in the current set of views | PS | (User-Defined) | (User-Defined) |
| Output | REFRRPT | Control Report | Yes | PS | VB | 164 |
| | REFRLOG  | Log File | Yes | PS | VB | 164 |
| | REFRTRAC | Trace File | If TRACE=Y | PS | VB | 164 |
| | REFRDUMP | Logic Table Dump File | If DUMP_LT_AND_ GENERATED_CODE=Y | PS | VB | 164 |
| | REFRSTAT | Run Statistics File | No | PS | FB | 80 |
| | REFRREH  | Reference Extract Header File | Yes | PS | FB | 100 |
| | REFRRTH  | Reference Title Header File | Yes | PS | FB | 100 |
| | REFR*nnn* | Reference-Phase Work File *nnn* | A file is required for every lookup table used in the current set of views | PS | VB | 4144 |

