# Unofficial Protocol Specification for Oracle Database

> **DISCLAIMER:** I, Jonah H. Harris, am an independent developer. While I have spoken about [Oracle Database](https://www.oracle.com/database/) at many conferences, including Oracle OpenWorld, I have never worked for, and this specification is in no way affiliated with, [Oracle Corporation](https://www.oracle.com/).
> 
> ***All information provided here is based on personal research; it is not supported or recommended by Oracle Corporation.***

## Table of Contents
- [Introduction](#introduction)
  * [Glossary](#glossary)
  * [References](#references)
    + [Normative References](#normative-references)
    + [Informative References](#informative-references)
  * [Overview](#overview)
  * [Relationship to Other Protocols](#relationship-to-other-protocols)
  * [Prerequisites/Preconditions](#prerequisitespreconditions)
  * [Applicability Statement](#applicability-statement)
  * [Versioning and Capability Negotiation](#versioning-and-capability-negotiation)
  * [Vendor-Extensible Fields](#vendor-extensible-fields)
  * [Standards Assignments](#standards-assignments)
- [Wire Protocol](#wire-protocol)
  * [Transport](#transport)
  * [Message Syntax](#message-syntax)
  * [Data Type Representation](#data-type-representation)
    + [Endianness](#endianness)
    + [Types](#types)
  * [Transparent Network Substrate (TNS)](#transparent-network-substrate-tns)
    + [Packet Header](#packet-header)
    + [Packet Types](#packet-types)
      - [0x01 - CoNnect (NSPTCN)](#0x01---connect-nsptcn)
      - [0x02 - ACcept (NSPTAC)](#0x02---accept-nsptac)
      - [0x03 - AcKnowledge (NSPTAK)](#0x03---acknowledge-nsptak)
      - [0x04 – ReFuse/ReFute (NSPTRF)](#0x04-%E2%80%93-refuserefute-nsptrf)
      - [0x05 - ReDirect (NSPTRD)](#0x05---redirect-nsptrd)
      - [0x06 - DAta (NSPTDA)](#0x06---data-nsptda)
      - [0x07 - NuLl (NSPTNL)](#0x07---null-nsptnl)
      - [0x09 - ABort (NSPTAB)](#0x09---abort-nsptab)
      - [0x0b - ReSend (NSPTRS)](#0x0b---resend-nsptrs)
      - [0x0c - MarKer (NSPTMK)](#0x0c---marker-nsptmk)
      - [0x0d - ATtention (NSPTAT)](#0x0d---attention-nsptat)
      - [0x0e - ControL (NSPCNL)](#0x0e---control-nspcnl)
      - [0x0f - Data Descriptor (NSPTDD)](#0x0f---data-descriptor-nsptdd)
  * [Two-Task Interface (TTI/TTC)](#two-task-interface-ttittc)
    + [0x01 - PROtocol Negotiation (TTIPRO)](#0x01---protocol-negotiation-ttipro)
    + [0x02 - Data TYpe Negotiation (TTIDTY)](#0x02---data-type-negotiation-ttidty)
    + [0x03 - FUNction Call (TTIFUN)](#0x03---function-call-ttifun)
    + [0x04 - Oracle Error/Return (TTIOER)](#0x04---oracle-errorreturn-ttioer)
    + [0x05 - Access User Address Space (TTIAUA)](#0x05---access-user-address-space-ttiaua)
    + [0x06 - Row Xfer Header (TTIRXH)](#0x06---row-xfer-header-ttirxh)
    + [0x07 - Row Xfer Data (TTIRXD)](#0x07---row-xfer-data-ttirxd)
    + [0x08 - Return PArameter (TTIRPA)](#0x08---return-parameter-ttirpa)
    + [0x09 - Function STAtus (TTISTA)](#0x09---function-status-ttista)
    + [0x0a - Number of OER (TTINOER)](#0x0a---number-of-oer-ttinoer)
    + [0x0b - I/O Vector (for UPI) (TTIIOV)](#0x0b---io-vector-for-upi-ttiiov)
    + [0x0c - Send LonG/User Data Send (TTISLG/TTIUDS)](#0x0c---send-longuser-data-send-ttislgttiuds)
    + [0x0d - Oracle ACcessor (TTIOAC)](#0x0d---oracle-accessor-ttioac)
    + [0x0e - LOB/BFILE Data (TTILOBD)](#0x0e---lobbfile-data-ttilobd)
    + [0x0f - WaRNing Messages (TTIWRN)](#0x0f---warning-messages-ttiwrn)
    + [0x10 - DesCriBe Info (TTIDCB)](#0x10---describe-info-ttidcb)
    + [0x11 - Piggyback FuNction (TTIPFN)](#0x11---piggyback-function-ttipfn)
    + [0x12 - Untrusted Callouts (TTI3GL)](#0x12---untrusted-callouts-tti3gl)
    + [0x13 - Flush Out Binds (TTIFOB)](#0x13---flush-out-binds-ttifob)
    + [0x15 - Bit VeCtor for Compressed Fetch (TTIBVC)](#0x15---bit-vector-for-compressed-fetch-ttibvc)
    + [0x16 - End Of Bind with Bind Row Callback (TTIEOB)](#0x16---end-of-bind-with-bind-row-callback-ttieob)
    + [0x17 - Server Piggyback Function (TTISPF)](#0x17---server-piggyback-function-ttispf)
    + [0x1a - Function with no ret (TTIONEWAYFN)](#0x1a---function-with-no-ret-ttionewayfn)
    + [0x1b - Implicit Result Set (TTIIMPLRES)](#0x1b---implicit-result-set-ttiimplres)
    + [0xde - Secure Network Svcs (TTISNS)](#0xde---secure-network-svcs-ttisns)
  * [Oracle Programmatic Interface (OPI)](#oracle-programmatic-interface-opi)
    + [0x01 - Logon to Oracle (OLOGON)](#0x01---logon-to-oracle-ologon)
    + [0x02 - Open Cursor (OOPEN)](#0x02---open-cursor-oopen)
    + [0x03 - Parse Statement (OPARSE)](#0x03---parse-statement-oparse)
    + [0x04 - Execute Statement (OEXEC)](#0x04---execute-statement-oexec)
    + [0x05 - Fetch Row (OFETCH)](#0x05---fetch-row-ofetch)
    + [0x06 - Bind by Name/Value (OBIND)](#0x06---bind-by-namevalue-obind)
    + [0x07 - Bind by Number (Position)/Value (OBINDN)](#0x07---bind-by-number-positionvalue-obindn)
    + [0x08 - Close Cursor (OCLOSE)](#0x08---close-cursor-oclose)
    + [0x09 - Logoff of Oracle (OLOGOFF)](#0x09---logoff-of-oracle-ologoff)
    + [0x0a - Describe Select List Column (ODSCRIBE)](#0x0a---describe-select-list-column-odscribe)
    + [0x0b - Define Column (ODEFIN)](#0x0b---define-column-odefin)
    + [0x0c - Autocommit ON (OCOMON)](#0x0c---autocommit-on-ocomon)
    + [0x0d - Autocommit OFF (OCOMOFF)](#0x0d---autocommit-off-ocomoff)
    + [0x0e - COMMIT (OCOMMIT)](#0x0e---commit-ocommit)
    + [0x0f - ROLLBACK (OROLLBACK)](#0x0f---rollback-orollback)
    + [0x10 - Set Fatal Error Options (OSFE)](#0x10---set-fatal-error-options-osfe)
    + [0x11 - Resume Operation (ORESUME)](#0x11---resume-operation-oresume)
    + [0x12 - Get Oracle Version & Date (OVERSN)](#0x12---get-oracle-version--date-oversn)
    + [0x13 - until we get rid of OASQL (OTEMP)](#0x13---until-we-get-rid-of-oasql-otemp)
    + [0x14 - Cancel Current Operation (OCANCEL)](#0x14---cancel-current-operation-ocancel)
    + [0x15 - Get Error Message (OGEM)](#0x15---get-error-message-ogem)
    + [0x16 - Exit Oracle (OEXIT)](#0x16---exit-oracle-oexit)
    + [0x17 - Special Function (OSPECIAL)](#0x17---special-function-ospecial)
    + [0x18 - ABORT (OABORT)](#0x18---abort-oabort)
    + [0x19 - Dequeue by ROWID (ODQRID)](#0x19---dequeue-by-rowid-odqrid)
    + [0x1a - Fetch Long Column Value (OLNGF6)](#0x1a---fetch-long-column-value-olngf6)
    + [0x1b - Create Access Module (OCAM)](#0x1b---create-access-module-ocam)
    + [0x1c - Save Access Module Statement (OSAMS)](#0x1c---save-access-module-statement-osams)
    + [0x1d - Save Access Module (OSAM)](#0x1d---save-access-module-osam)
    + [0x1e - Parse Access Module Statement (OPAMS)](#0x1e---parse-access-module-statement-opams)
    + [0x1f - How Many Items (OHOWMANY)](#0x1f---how-many-items-ohowmany)
    + [0x20 - Initialize Oracle (OINIT)](#0x20---initialize-oracle-oinit)
    + [0x21 - Change User (OCHANGEU)](#0x21---change-user-ochangeu)
    + [0x22 - Bind by Reference Position (OBINDRP)](#0x22---bind-by-reference-position-obindrp)
    + [0x23 - Get Bind Variable (OGETBV)](#0x23---get-bind-variable-ogetbv)
    + [0x24 - Get Into Variable (OGETIV)](#0x24---get-into-variable-ogetiv)
    + [0x25 - Bind by Reference (OBINDRV)](#0x25---bind-by-reference-obindrv)
    + [0x26 - Bind by Reference Numeric (OBINDRN)](#0x26---bind-by-reference-numeric-obindrn)
    + [0x27 - Parse & Execute (OPARSEX)](#0x27---parse--execute-oparsex)
    + [0x28 - Parse (Syntax Only) (OPARSYN)](#0x28---parse-syntax-only-oparsyn)
    + [0x29 - Parse for Syntax & Dictionary (OPARSDI)](#0x29---parse-for-syntax--dictionary-oparsdi)
    + [0x2a - Continue after EOF (OCONTINUE)](#0x2a---continue-after-eof-ocontinue)
    + [0x2b - Describe Array (ODSCRARR)](#0x2b---describe-array-odscrarr)
    + [0x2c - Initialize System Parameter (OLCCINI)](#0x2c---initialize-system-parameter-olccini)
    + [0x2d - Finalize System Parameter (OLCCFIN)](#0x2d---finalize-system-parameter-olccfin)
    + [0x2e - Put System Parameter (OLCCPUT)](#0x2e---put-system-parameter-olccput)
    + [0x2f - Get System Parameter Info (OLCCGPI)](#0x2f---get-system-parameter-info-olccgpi)
    + [0x30 - Start Oracle (V6) (OV6STRT)](#0x30---start-oracle-v6-ov6strt)
    + [0x31 - Stop Oracle (V6) (OV6STOP)](#0x31---stop-oracle-v6-ov6stop)
    + [0x32 - Run Independent Process (V6) (ORIP)](#0x32---run-independent-process-v6-orip)
    + [0x33 - Test RAM (V6) (OTRAM)](#0x33---test-ram-v6-otram)
    + [0x34 - Archive Operation (V6) (OARCHIVE)](#0x34---archive-operation-v6-oarchive)
    + [0x35 - Media Recovery - Start (V6) (OMRSTART)](#0x35---media-recovery---start-v6-omrstart)
    + [0x36 - MR - Record Tablespace (V6) (OMRRECTS)](#0x36---mr---record-tablespace-v6-omrrects)
    + [0x37 - MR - Get Starting LSN (V6) (OMRGSLSQ)](#0x37---mr---get-starting-lsn-v6-omrgslsq)
    + [0x38 - MR - Recover w Offline Log (V6) (OMRREC)](#0x38---mr---recover-w-offline-log-v6-omrrec)
    + [0x39 - MR - Cancel Recovery (V6) (OMRCAN)](#0x39---mr---cancel-recovery-v6-omrcan)
    + [0x3a - Logon to Oracle (V6) (O2LOGON)](#0x3a---logon-to-oracle-v6-o2logon)
    + [0x3b - Get Version & Date (New Format) (OVERSION)](#0x3b---get-version--date-new-format-oversion)
    + [0x3c - Initialize Oracle (New) (OINIT2)](#0x3c---initialize-oracle-new-oinit2)
    + [0x3d - Close All Cursors (MAC-Only) (OCLOALL)](#0x3d---close-all-cursors-mac-only-ocloall)
    + [0x3e - Bundled Execution Call (OALL)](#0x3e---bundled-execution-call-oall)
    + [0x3f - Transaction Execute (DOS (OTEX)](#0x3f---transaction-execute-dos-otex)
    + [0x40 - Set DBA Authorization (DOS (OSDAUTH)](#0x40---set-dba-authorization-dos-osdauth)
    + [0x41 - Direct Loader Functions (OUDLFUN)](#0x41---direct-loader-functions-oudlfun)
    + [0x42 - Direct Loader Buffer Transfer (OUDLBUF)](#0x42---direct-loader-buffer-transfer-oudlbuf)
    + [0x43 - Distributed Transaction Manager RPC (OK2RPC)](#0x43---distributed-transaction-manager-rpc-ok2rpc)
    + [0x44 - Distributed Describe Indexes (ODSCIDX)](#0x44---distributed-describe-indexes-odscidx)
    + [0x45 - Session Operations (OSESOPN)](#0x45---session-operations-osesopn)
    + [0x46 - Execute using Synchronized SCNs (OEXECSCN)](#0x46---execute-using-synchronized-scns-oexecscn)
    + [0x47 - Fast UPI calls to OPIAL7 (OALL7)](#0x47---fast-upi-calls-to-opial7-oall7)
    + [0x48 - Long Fetch (V7) (OLONGF)](#0x48---long-fetch-v7-olongf)
    + [0x49 - OPIEXE via OPIALL (OEXECA)](#0x49---opiexe-via-opiall-oexeca)
    + [0x4a - Parse SQL (V7) (OSQL7)](#0x4a---parse-sql-v7-osql7)
    + [0x4b - Please DO Not REUSE THIS CODE (OOBS)](#0x4b---please-do-not-reuse-this-code-oobs)
    + [0x4c - RPC via PL/SQL (ORPC)](#0x4c---rpc-via-plsql-orpc)
    + [0x4d - Kernel Generic Library Op (OKGL_OLD)](#0x4d---kernel-generic-library-op-okgl_old)
    + [0x4e - Execute & Fetch # (OEXFEN)](#0x4e---execute--fetch-%23-oexfen)
    + [0x4f - X/Open XA Operation (OXAOPN)](#0x4f---xopen-xa-operation-oxaopn)
    + [0x50 - Kernel Generic Library Op (New) (OKGL)](#0x50---kernel-generic-library-op-new-okgl)
    + [0x51 - Logon (Part 2) (O3LOGON)](#0x51---logon-part-2-o3logon)
    + [0x52 - Logon (Part 1) (O3LOGA)](#0x52---logon-part-1-o3loga)
    + [0x53 - Do Streaming Operation (OFNSTM)](#0x53---do-streaming-operation-ofnstm)
    + [0x54 - Open Session (V71) (O71SESOPN)](#0x54---open-session-v71-o71sesopn)
    + [0x55 - X/Open XA Operation (V71) (O71XAOPN)](#0x55---xopen-xa-operation-v71-o71xaopn)
    + [0x56 - Debugging Operation (ODEBUG)](#0x56---debugging-operation-odebug)
    + [0x57 - Special Debugging Operation (ODEBUGS)](#0x57---special-debugging-operation-odebugs)
    + [0x58 - XA Start (OXAST)](#0x58---xa-start-oxast)
    + [0x59 - XA Switch & Commit (OXACM)](#0x59---xa-switch--commit-oxacm)
    + [0x5a - XA Switch & Prepare (OXAPR)](#0x5a---xa-switch--prepare-oxapr)
    + [0x5b - XA Direct Buffer Copy (OXDP)](#0x5b---xa-direct-buffer-copy-oxdp)
    + [0x5c - KOD Call (New) (OKOD)](#0x5c---kod-call-new-okod)
    + [0x5d - Kernel Callback (OCBK)](#0x5d---kernel-callback-ocbk)
    + [0x5e - Bundled Call (V8) (OALL8)](#0x5e---bundled-call-v8-oall8)
    + [0x5f - OFNSTM (Without BEGIN) (OFNSTM2)](#0x5f---ofnstm-without-begin-ofnstm2)
    + [0x60 - LOB and FILE Operations (OLOBOPS)](#0x60---lob-and-file-operations-olobops)
    + [0x61 - FILE Create (OFILECRT)](#0x61---file-create-ofilecrt)
    + [0x62 - Describe Any (ODNY)](#0x62---describe-any-odny)
    + [0x63 - Non-blocking Attach (OCONNECT)](#0x63---non-blocking-attach-oconnect)
    + [0x64 - Open Recursive Cursor (OOPENRCS)](#0x64---open-recursive-cursor-oopenrcs)
    + [0x65 - Bundled KPR Call (OKPRALL)](#0x65---bundled-kpr-call-okprall)
    + [0x66 - Bundled PL/SQL Call (OPLS)](#0x66---bundled-plsql-call-opls)
    + [0x67 - Transaction Start (OTXSE)](#0x67---transaction-start-otxse)
    + [0x68 - Transaction End (OTXEN)](#0x68---transaction-end-otxen)
    + [0x69 - Cursor Close All (OCCA)](#0x69---cursor-close-all-occa)
    + [0x6a - Failover Info (OFOI)](#0x6a---failover-info-ofoi)
    + [0x6b - Session Switch (V8) (O80SES)](#0x6b---session-switch-v8-o80ses)
    + [0x6c - Do Dummy Defines (ODDF)](#0x6c---do-dummy-defines-oddf)
    + [0x6d - Initialize System Parameters (OLRMINI)](#0x6d---initialize-system-parameters-olrmini)
    + [0x6e - Finalize System Parameters (OLRMFIN)](#0x6e---finalize-system-parameters-olrmfin)
    + [0x6f - Put System Parameter (OLRMPUT)](#0x6f---put-system-parameter-olrmput)
    + [0x70 - Terminate System Parameters (OLRMTRM)](#0x70---terminate-system-parameters-olrmtrm)
    + [0x71 - Execute NO UNMAP (OPIALL0) (OEXFENA)](#0x71---execute-no-unmap-opiall0-oexfena)
    + [0x72 - OINIT for Untrusted Callbacks (OINIUCB)](#0x72---oinit-for-untrusted-callbacks-oiniucb)
    + [0x73 - Generic Authentication (OAUTH)](#0x73---generic-authentication-oauth)
    + [0x74 - Failover Get Instance Info (OFGI)](#0x74---failover-get-instance-info-ofgi)
    + [0x75 - OTS COMMIT Remote (OOTCO)](#0x75---ots-commit-remote-ootco)
    + [0x76 - Get Session Key (OSESSKEY)](#0x76---get-session-key-osesskey)
    + [0x77 - Describe Any (V8) (ODSY)](#0x77---describe-any-v8-odsy)
    + [0x78 - Cancel All (OCANA)](#0x78---cancel-all-ocana)
    + [0x79 - AQ Enqueue (OAQEQ)](#0x79---aq-enqueue-oaqeq)
    + [0x7a - AQ Dequeue (OAQDQ)](#0x7a---aq-dequeue-oaqdq)
    + [0x7b - RFS (ORFS)](#0x7b---rfs-orfs)
    + [0x7d - Kernel Prog Notification (OKPN)](#0x7d---kernel-prog-notification-okpn)
    + [0x7e - AQ Listen (OAQLS)](#0x7e---aq-listen-oaqls)
    + [0x7f - OTS COMMIT Remote Sites (V813) (OOTCM)](#0x7f---ots-commit-remote-sites-v813-ootcm)
    + [0x80 - Direct Path Prepare (ODPP)](#0x80---direct-path-prepare-odpp)
    + [0x81 - Direct Path Load Stream (ODPLS)](#0x81---direct-path-load-stream-odpls)
    + [0x82 - Direct Path Misc. Operations (ODPMOP)](#0x82---direct-path-misc-operations-odpmop)
    + [0x83 - Memory Statistics (OMEMSTA)](#0x83---memory-statistics-omemsta)
    + [0x84 - AQ Get Property Status (OAQGPS)](#0x84---aq-get-property-status-oaqgps)
    + [0x85 - Fast Path Execute/Fetch (OFPT)](#0x85---fast-path-executefetch-ofpt)
    + [0x86 - Fetch Archive Log (OFAL)](#0x86---fetch-archive-log-ofal)
    + [0x87 - Set Client ID (OSCID)](#0x87---set-client-id-oscid)
    + [0x88 - DR Server Connection Process (ODRCX)](#0x88---dr-server-connection-process-odrcx)
    + [0x89 - Fetch (New) (OFETCH2)](#0x89---fetch-new-ofetch2)
    + [0x8a - SPFILE Parameter Put (OSPFPPUT)](#0x8a---spfile-parameter-put-ospfpput)
    + [0x8b - Fast Connection Exchange (OKPFC)](#0x8b---fast-connection-exchange-okpfc)
    + [0x8c - Kernel Generic Transfer (V82) (OKGT)](#0x8c---kernel-generic-transfer-v82-okgt)
    + [0x8d - Push Transaction on Stack (OPUSHTX)](#0x8d---push-transaction-on-stack-opushtx)
    + [0x8e - Pop Transaction off Stack (OPOPTX)](#0x8e---pop-transaction-off-stack-opoptx)
    + [0x8f - KFN Operation (OKFN)](#0x8f---kfn-operation-okfn)
    + [0x90 - Direct Path Unload Stream (ODPULS)](#0x90---direct-path-unload-stream-odpuls)
    + [0x91 - AQ Array Enqueue/Dequeue (OAQXQ)](#0x91---aq-array-enqueuedequeue-oaqxq)
    + [0x92 - File Transfer (OFILXFR)](#0x92---file-transfer-ofilxfr)
    + [0x93 - Ping (OPING)](#0x93---ping-oping)
    + [0x94 - TSM (OTSM)](#0x94---tsm-otsm)
    + [0x95 - ? (OOPNC)](#0x95----oopnc)
    + [0x96 - Begin TSM (OBTSM)](#0x96---begin-tsm-obtsm)
    + [0x97 - End TSM (OETSM)](#0x97---end-tsm-oetsm)
    + [0x98 - Set Schema (OSCS)](#0x98---set-schema-oscs)
    + [0x99 - Fetch from suspended result-set (O?)](#0x99---fetch-from-suspended-result-set-o)
    + [0x9a - Key/Value Pair (OKEYVAL)](#0x9a---keyvalue-pair-okeyval)
    + [0x9b - XS Create Session (OXSSCS)](#0x9b---xs-create-session-oxsscs)
    + [0x9c - XS Session Operation (OXSSRO)](#0x9c---xs-session-operation-oxssro)
    + [0x9d - XS Async (Piggyback) Operation (OXSSPO)](#0x9d---xs-async-piggyback-operation-oxsspo)
    + [0x9e - KSRPC Execution (O?)](#0x9e---ksrpc-execution-o)
    + [0x9f - Streams combined capture/apply (O?)](#0x9f---streams-combined-captureapply-o)
    + [0xa0 - AQ replay information (O?)](#0xa0---aq-replay-information-o)
    + [0xa1 - SSCR (O?)](#0xa1---sscr-o)
    + [0xa2 - Get a Session (OSESSGET)](#0xa2---get-a-session-osessget)
    + [0xa3 - Release a Session (OSESSRLS)](#0xa3---release-a-session-osessrls)
    + [0xa5 - workload replay data (O?)](#0xa5---workload-replay-data-o)
    + [0xa6 - replay statistic data (O?)](#0xa6---replay-statistic-data-o)
    + [0xa7 - Query Cache Stats (O?)](#0xa7---query-cache-stats-o)
    + [0xa8 - Query Cache IDs (O?)](#0xa8---query-cache-ids-o)
    + [0xa9 - RPC Test Stream (O?)](#0xa9---rpc-test-stream-o)
    + [0xaa - replay plsql rpc (O?)](#0xaa---replay-plsql-rpc-o)
    + [0xab - XStream Out (O?)](#0xab---xstream-out-o)
    + [0xac - Golden Gate RPC (OXSNSO)](#0xac---golden-gate-rpc-oxsnso)
    + [0xb0 - Session State (OSESSSTATE)](#0xb0---session-state-osessstate)
    + [0xb1 - Application Continuity Replay (OAPPCONTREPLAY)](#0xb1---application-continuity-replay-oappcontreplay)
    + [0xb2 - ? (OXSNS)](#0xb2----oxsns)
    + [0xb3 - ? (OXSCRE)](#0xb3----oxscre)
    + [0xb4 - ? (OXSATT)](#0xb4----oxsatt)
    + [0xb5 - ? (OXSDET)](#0xb5----oxsdet)
    + [0xb6 - ? (OXSDES)](#0xb6----oxsdes)
    + [0xb7 - ? (OXSSET)](#0xb7----oxsset)
    + [0xb8 - ? (OAQENQ)](#0xb8----oaqenq)
    + [0xb9 - ? (OAQDEQ)](#0xb9----oaqdeq)
    + [0xba - ? (OAQEMNDEQ)](#0xba----oaqemndeq)
    + [0xbb - Session State (OAQNFY)](#0xbb---session-state-oaqnfy)
- [Protocol Details](#protocol-details)
- [Protocol Examples](#protocol-examples)

## Introduction

### Glossary

### References

#### Normative References

#### Informative References

### Overview

### Relationship to Other Protocols

### Prerequisites/Preconditions

### Applicability Statement

### Versioning and Capability Negotiation

### Vendor-Extensible Fields

### Standards Assignments

## Wire Protocol

### Transport

### Message Syntax

### Data Type Representation

#### Endianness

#### Types

### Transparent Network Substrate (TNS)

#### Packet Header

#### Packet Types

##### 0x01 - CoNnect (NSPTCN)

###### Purpose

This is the first packet used to establish a TNS-level connection to Oracle Database.

###### Versions

- 5.x – Current

###### Direction

- Client to Server

###### Comments

- As one would expect, just as it is sent from the client to the server, this packet is also used in server-to-server communications for items such as database links.

###### Rules

```
nspcnvsn      = uint16_t
nspcnlov      = uint16_t
nspcnopt      = uint16_t
nspcnsdu      = uint16_t
nspcntdu      = uint16_t
nspcnntc      = uint16_t
nspcntna      = uint16_t
nspcnone      = uint16_t
nspcnlen      = uint16_t
nspcnoff      = uint16_t
nspcnmxc      = uint32_t
nspcnfl0      = uint8_t
nspcnfl1      = uint8_t
nspcncf1      = uint32_t
nspcncf2      = uint32_t
nspcncid      = uint8_t[8]
nspcncix      = uint8_t[8]
nspcndat      = ng_buffer_h[nspcnlen]
```

###### Definition

```
nspcnvsn
nspcnlov
nspcnopt
nspcnsdu
nspcntdu
nspcnntc
nspcntna
nspcnone
nspcnlen
nspcnoff
nspcnmxc
nspcnfl0
nspcnfl1
nspcncf1
nspcncf2
nspcncid
nspcncix
nspcndat
```

###### Details

| Parameter | Description |
|-----------|-------------|
| nspcnvsn | Packet Version |
| nspcnlov | Lowest Compatible Version |
| nspcnopt | Supports Global Service Options |
| nspcnsdu | Session Data Unit Size (in bytes) |
| nspcntdu | Transport Data Unit Size (in bytes) |
| nspcnntc | NT Protocol Characteristics |
| nspcntna | Line Turnaround Value |
| nspcnone | The number 1 in Host Byte Order |
| nspcnlen | Length of Connect Data (in bytes) |
| nspcnoff | Byte Offset to Connect Data |
| nspcnmxc | Maximum Connect Data |
| nspcnfl0 | Connect Flags 0 |
| nspcnfl1 | Connect Flags 1 |
| nspcncf1 | Cross Facility Item 1 |
| nspcncf2 | Cross Facility Item 2 |
| nspcncid | Unique Connection Id |
| nspcncix | Unique Connection Id |
| nspcndat | Connect Data (in Oracle NVP format) |

###### Response Packets

- NSPTRS
- NSPTRD
- NSPTRF
- NSPTAC

##### 0x02 - ACcept (NSPTAC)

##### 0x03 - AcKnowledge (NSPTAK)

##### 0x04 – ReFuse/ReFute (NSPTRF)

##### 0x05 - ReDirect (NSPTRD)

##### 0x06 - DAta (NSPTDA)

##### 0x07 - NuLl (NSPTNL)

###### Purpose

This packet is a no-op used to keep a connection alive.

###### Versions

- 5.x – Current

###### Direction

- Client to Server
- Server to Client?

###### Comments

- While this has only been seen sent from server to client, it could, theoretically, be sent in either direction.

###### Rules

###### Definition

###### Details

- This packet has no data and is simply identified by the packet type in the TNS packet header.

###### Response Packets

None.

##### 0x09 - ABort (NSPTAB)

##### 0x0b - ReSend (NSPTRS)

###### Purpose

This packet is used to request retransmission of the previous TNS packet.

###### Versions

- 5.x – Current

###### Direction

- Client to Server
- Server to Client?

###### Comments

- While this has only been seen sent from server to client, it could, theoretically, be sent in either direction.

###### Rules

###### Definition

###### Details

- This packet has no data and is simply identified by the packet type in the TNS packet header.

###### Response Packets

Previous TNS packet transmitted.

##### 0x0c - MarKer (NSPTMK)

##### 0x0d - ATtention (NSPTAT)

##### 0x0e - ControL (NSPCNL)

##### 0x0f - Data Descriptor (NSPTDD)

### Two-Task Interface (TTI/TTC)

#### 0x01 - PROtocol Negotiation (TTIPRO)

#### 0x02 - Data TYpe Negotiation (TTIDTY)

#### 0x03 - FUNction Call (TTIFUN)

#### 0x04 - Oracle Error/Return (TTIOER)

#### 0x05 - Access User Address Space (TTIAUA)

#### 0x06 - Row Xfer Header (TTIRXH)

#### 0x07 - Row Xfer Data (TTIRXD)

#### 0x08 - Return PArameter (TTIRPA)

#### 0x09 - Function STAtus (TTISTA)

#### 0x0a - Number of OER (TTINOER)

#### 0x0b - I/O Vector (for UPI) (TTIIOV)

#### 0x0c - Send LonG/User Data Send (TTISLG/TTIUDS)

#### 0x0d - Oracle ACcessor (TTIOAC)

#### 0x0e - LOB/BFILE Data (TTILOBD)

#### 0x0f - WaRNing Messages (TTIWRN)

#### 0x10 - DesCriBe Info (TTIDCB)

#### 0x11 - Piggyback FuNction (TTIPFN)

#### 0x12 - Untrusted Callouts (TTI3GL)

#### 0x13 - Flush Out Binds (TTIFOB)

#### 0x15 - Bit VeCtor for Compressed Fetch (TTIBVC)

#### 0x16 - End Of Bind with Bind Row Callback (TTIEOB)

#### 0x17 - Server Piggyback Function (TTISPF)

#### 0x1a - Function with no ret (TTIONEWAYFN)

#### 0x1b - Implicit Result Set (TTIIMPLRES)

#### 0xde - Secure Network Svcs (TTISNS)

### Oracle Programmatic Interface (OPI)

#### 0x01 - Logon to Oracle (OLOGON)

#### 0x02 - Open Cursor (OOPEN)

#### 0x03 - Parse Statement (OPARSE)

#### 0x04 - Execute Statement (OEXEC)

#### 0x05 - Fetch Row (OFETCH)

#### 0x06 - Bind by Name/Value (OBIND)

#### 0x07 - Bind by Number (Position)/Value (OBINDN)

#### 0x08 - Close Cursor (OCLOSE)

#### 0x09 - Logoff of Oracle (OLOGOFF)

#### 0x0a - Describe Select List Column (ODSCRIBE)

#### 0x0b - Define Column (ODEFIN)

#### 0x0c - Autocommit ON (OCOMON)

#### 0x0d - Autocommit OFF (OCOMOFF)

#### 0x0e - COMMIT (OCOMMIT)

#### 0x0f - ROLLBACK (OROLLBACK)

#### 0x10 - Set Fatal Error Options (OSFE)

#### 0x11 - Resume Operation (ORESUME)

#### 0x12 - Get Oracle Version & Date (OVERSN)

#### 0x13 - until we get rid of OASQL (OTEMP)

#### 0x14 - Cancel Current Operation (OCANCEL)

#### 0x15 - Get Error Message (OGEM)

#### 0x16 - Exit Oracle (OEXIT)

#### 0x17 - Special Function (OSPECIAL)

#### 0x18 - ABORT (OABORT)

#### 0x19 - Dequeue by ROWID (ODQRID)

#### 0x1a - Fetch Long Column Value (OLNGF6)

#### 0x1b - Create Access Module (OCAM)

#### 0x1c - Save Access Module Statement (OSAMS)

#### 0x1d - Save Access Module (OSAM)

#### 0x1e - Parse Access Module Statement (OPAMS)

#### 0x1f - How Many Items (OHOWMANY)

#### 0x20 - Initialize Oracle (OINIT)

#### 0x21 - Change User (OCHANGEU)

#### 0x22 - Bind by Reference Position (OBINDRP)

#### 0x23 - Get Bind Variable (OGETBV)

#### 0x24 - Get Into Variable (OGETIV)

#### 0x25 - Bind by Reference (OBINDRV)

#### 0x26 - Bind by Reference Numeric (OBINDRN)

#### 0x27 - Parse & Execute (OPARSEX)

#### 0x28 - Parse (Syntax Only) (OPARSYN)

#### 0x29 - Parse for Syntax & Dictionary (OPARSDI)

#### 0x2a - Continue after EOF (OCONTINUE)

#### 0x2b - Describe Array (ODSCRARR)

#### 0x2c - Initialize System Parameter (OLCCINI)

#### 0x2d - Finalize System Parameter (OLCCFIN)

#### 0x2e - Put System Parameter (OLCCPUT)

#### 0x2f - Get System Parameter Info (OLCCGPI)

#### 0x30 - Start Oracle (V6) (OV6STRT)

#### 0x31 - Stop Oracle (V6) (OV6STOP)

#### 0x32 - Run Independent Process (V6) (ORIP)

#### 0x33 - Test RAM (V6) (OTRAM)

#### 0x34 - Archive Operation (V6) (OARCHIVE)

#### 0x35 - Media Recovery - Start (V6) (OMRSTART)

#### 0x36 - MR - Record Tablespace (V6) (OMRRECTS)

#### 0x37 - MR - Get Starting LSN (V6) (OMRGSLSQ)

#### 0x38 - MR - Recover w Offline Log (V6) (OMRREC)

#### 0x39 - MR - Cancel Recovery (V6) (OMRCAN)

#### 0x3a - Logon to Oracle (V6) (O2LOGON)

#### 0x3b - Get Version & Date (New Format) (OVERSION)

#### 0x3c - Initialize Oracle (New) (OINIT2)

#### 0x3d - Close All Cursors (MAC-Only) (OCLOALL)

#### 0x3e - Bundled Execution Call (OALL)

#### 0x3f - Transaction Execute (DOS (OTEX)

#### 0x40 - Set DBA Authorization (DOS (OSDAUTH)

#### 0x41 - Direct Loader Functions (OUDLFUN)

#### 0x42 - Direct Loader Buffer Transfer (OUDLBUF)

#### 0x43 - Distributed Transaction Manager RPC (OK2RPC)

#### 0x44 - Distributed Describe Indexes (ODSCIDX)

#### 0x45 - Session Operations (OSESOPN)

#### 0x46 - Execute using Synchronized SCNs (OEXECSCN)

#### 0x47 - Fast UPI calls to OPIAL7 (OALL7)

#### 0x48 - Long Fetch (V7) (OLONGF)

#### 0x49 - OPIEXE via OPIALL (OEXECA)

#### 0x4a - Parse SQL (V7) (OSQL7)

#### 0x4b - Please DO Not REUSE THIS CODE (OOBS)

#### 0x4c - RPC via PL/SQL (ORPC)

#### 0x4d - Kernel Generic Library Op (OKGL_OLD)

#### 0x4e - Execute & Fetch # (OEXFEN)

#### 0x4f - X/Open XA Operation (OXAOPN)

#### 0x50 - Kernel Generic Library Op (New) (OKGL)

#### 0x51 - Logon (Part 2) (O3LOGON)

#### 0x52 - Logon (Part 1) (O3LOGA)

#### 0x53 - Do Streaming Operation (OFNSTM)

#### 0x54 - Open Session (V71) (O71SESOPN)

#### 0x55 - X/Open XA Operation (V71) (O71XAOPN)

#### 0x56 - Debugging Operation (ODEBUG)

#### 0x57 - Special Debugging Operation (ODEBUGS)

#### 0x58 - XA Start (OXAST)

#### 0x59 - XA Switch & Commit (OXACM)

#### 0x5a - XA Switch & Prepare (OXAPR)

#### 0x5b - XA Direct Buffer Copy (OXDP)

#### 0x5c - KOD Call (New) (OKOD)

#### 0x5d - Kernel Callback (OCBK)

#### 0x5e - Bundled Call (V8) (OALL8)

#### 0x5f - OFNSTM (Without BEGIN) (OFNSTM2)

#### 0x60 - LOB and FILE Operations (OLOBOPS)

#### 0x61 - FILE Create (OFILECRT)

#### 0x62 - Describe Any (ODNY)

#### 0x63 - Non-blocking Attach (OCONNECT)

#### 0x64 - Open Recursive Cursor (OOPENRCS)

#### 0x65 - Bundled KPR Call (OKPRALL)

#### 0x66 - Bundled PL/SQL Call (OPLS)

#### 0x67 - Transaction Start (OTXSE)

#### 0x68 - Transaction End (OTXEN)

#### 0x69 - Cursor Close All (OCCA)

#### 0x6a - Failover Info (OFOI)

#### 0x6b - Session Switch (V8) (O80SES)

#### 0x6c - Do Dummy Defines (ODDF)

#### 0x6d - Initialize System Parameters (OLRMINI)

#### 0x6e - Finalize System Parameters (OLRMFIN)

#### 0x6f - Put System Parameter (OLRMPUT)

#### 0x70 - Terminate System Parameters (OLRMTRM)

#### 0x71 - Execute NO UNMAP (OPIALL0) (OEXFENA)

#### 0x72 - OINIT for Untrusted Callbacks (OINIUCB)

#### 0x73 - Generic Authentication (OAUTH)

#### 0x74 - Failover Get Instance Info (OFGI)

#### 0x75 - OTS COMMIT Remote (OOTCO)

#### 0x76 - Get Session Key (OSESSKEY)

#### 0x77 - Describe Any (V8) (ODSY)

#### 0x78 - Cancel All (OCANA)

#### 0x79 - AQ Enqueue (OAQEQ)

#### 0x7a - AQ Dequeue (OAQDQ)

#### 0x7b - RFS (ORFS)

#### 0x7d - Kernel Prog Notification (OKPN)

#### 0x7e - AQ Listen (OAQLS)

#### 0x7f - OTS COMMIT Remote Sites (V813) (OOTCM)

#### 0x80 - Direct Path Prepare (ODPP)

#### 0x81 - Direct Path Load Stream (ODPLS)

#### 0x82 - Direct Path Misc. Operations (ODPMOP)

#### 0x83 - Memory Statistics (OMEMSTA)

#### 0x84 - AQ Get Property Status (OAQGPS)

#### 0x85 - Fast Path Execute/Fetch (OFPT)

#### 0x86 - Fetch Archive Log (OFAL)

#### 0x87 - Set Client ID (OSCID)

#### 0x88 - DR Server Connection Process (ODRCX)

#### 0x89 - Fetch (New) (OFETCH2)

#### 0x8a - SPFILE Parameter Put (OSPFPPUT)

#### 0x8b - Fast Connection Exchange (OKPFC)

#### 0x8c - Kernel Generic Transfer (V82) (OKGT)

#### 0x8d - Push Transaction on Stack (OPUSHTX)

#### 0x8e - Pop Transaction off Stack (OPOPTX)

#### 0x8f - KFN Operation (OKFN)

#### 0x90 - Direct Path Unload Stream (ODPULS)

#### 0x91 - AQ Array Enqueue/Dequeue (OAQXQ)

#### 0x92 - File Transfer (OFILXFR)

#### 0x93 - Ping (OPING)

#### 0x94 - TSM (OTSM)

#### 0x95 - ? (OOPNC)

#### 0x96 - Begin TSM (OBTSM)

#### 0x97 - End TSM (OETSM)

#### 0x98 - Set Schema (OSCS)

#### 0x99 - Fetch from suspended result-set (O?)

#### 0x9a - Key/Value Pair (OKEYVAL)

#### 0x9b - XS Create Session (OXSSCS)

#### 0x9c - XS Session Operation (OXSSRO)

#### 0x9d - XS Async (Piggyback) Operation (OXSSPO)

#### 0x9e - KSRPC Execution (O?)

#### 0x9f - Streams combined capture/apply (O?)

#### 0xa0 - AQ replay information (O?)

#### 0xa1 - SSCR (O?)

#### 0xa2 - Get a Session (OSESSGET)

#### 0xa3 - Release a Session (OSESSRLS)

#### 0xa5 - workload replay data (O?)

#### 0xa6 - replay statistic data (O?)

#### 0xa7 - Query Cache Stats (O?)

#### 0xa8 - Query Cache IDs (O?)

#### 0xa9 - RPC Test Stream (O?)

#### 0xaa - replay plsql rpc (O?)

#### 0xab - XStream Out (O?)

#### 0xac - Golden Gate RPC (OXSNSO)

#### 0xb0 - Session State (OSESSSTATE)

#### 0xb1 - Application Continuity Replay (OAPPCONTREPLAY)

#### 0xb2 - ? (OXSNS)

#### 0xb3 - ? (OXSCRE)

#### 0xb4 - ? (OXSATT)

#### 0xb5 - ? (OXSDET)

#### 0xb6 - ? (OXSDES)

#### 0xb7 - ? (OXSSET)

#### 0xb8 - ? (OAQENQ)

#### 0xb9 - ? (OAQDEQ)

#### 0xba - ? (OAQEMNDEQ)

#### 0xbb - Session State (OAQNFY)

## Protocol Details

## Protocol Examples

