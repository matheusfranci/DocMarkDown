# Correção de erro comum na aplicação do DBRU

### Como o erro ocorre:
```bash
>$ORACLE_HOME/OPatch/opatch apply -local -silent $INSTALL_19C/$DBRU
```

```log
Oracle Interim Patch Installer version 12.2.0.1.45
Copyright (c) 2025, Oracle Corporation.  All rights reserved.


Oracle Home       : /u01/app/oracle/product/19c/dbhome_1
Central Inventory : /u01/app/oracle/oraInventory
   from           : /u01/app/oracle/product/19c/dbhome_1/oraInst.loc
OPatch version    : 12.2.0.1.45
OUI version       : 12.2.0.7.0
Log file location : /u01/app/oracle/product/19c/dbhome_1/cfgtoollogs/opatch/opatch2025-02-25_13-31-41PM_1.log

Verifying environment and performing prerequisite checks...
```

```log
Conflicts/Supersets for each patch are:

Patch : 37260974

UtilSession failed: Reason -
Superset Patch 37260974 has
Subset Patch 36233263 which has overlay patches [27605010] and these overlay patches conflict with Superset Patch or are not included in Superest Patch

  - Please rollback the relevant overlay patches of the subset patches and apply the superset patch
Log file location: /u01/app/oracle/product/19c/dbhome_1/cfgtoollogs/opatch/opatch2025-02-25_13-31-41PM_1.log

OPatch failed with error code 73
```

### Resolução( O ID está entre colchetes na linha 30)
```bash
opatch rollback -id 27605010
```

```log
Oracle Interim Patch Installer version 12.2.0.1.45
Copyright (c) 2025, Oracle Corporation.  All rights reserved.


Oracle Home       : /u01/app/oracle/product/19c/dbhome_1
Central Inventory : /u01/app/oracle/oraInventory
   from           : /u01/app/oracle/product/19c/dbhome_1/oraInst.loc
OPatch version    : 12.2.0.1.45
OUI version       : 12.2.0.7.0
Log file location : /u01/app/oracle/product/19c/dbhome_1/cfgtoollogs/opatch/opatch2025-02-25_13-36-37PM_1.log


Patches will be rolled back in the following order:
   27605010
The following patch(es) will be rolled back: 27605010

Rolling back patch 27605010...

RollbackSession rolling back interim patch '27605010' from OH '/u01/app/oracle/product/19c/dbhome_1'

Patching component oracle.rdbms.dbscripts, 19.0.0.0.0...
RollbackSession removing interim patch '27605010' from inventory
Log file location: /u01/app/oracle/product/19c/dbhome_1/cfgtoollogs/opatch/opatch2025-02-25_13-36-37PM_1.log

OPatch succeeded.
```

### Agora é só reaplicar meu consagrado(a).
