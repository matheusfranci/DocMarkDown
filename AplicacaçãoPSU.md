# Processo de Aplicação de PSU em Oracle Database 19c

Este documento descreve o processo de aplicação de um Patch Set Update (PSU) em um banco de dados Oracle 19c, tanto para ambientes Cluster (CRS) quanto para Single Instance (HAS).

## Pré-requisitos

### Copiar a pasta do PSU para a máquina destino
```bash
scp -r /install/oracle/psu/janeiro/ oracle@HostDeDestinoAmigão:/install/oracle/psu/
```
Copia recursivamente a pasta do PSU (janeiro) do servidor local para o servidor destino.

### Atualizar o OPatch (Usuário GRID e Oracle)
```bash
cd $ORACLE_HOME/OPatch/
rm -rf *
unzip /install/oracle/psu/janeiro/p6880880_190000_Linux-x86-64.zip -d $ORACLE_HOME
```
Atualiza o OPatch, removendo a versão existente e descompactando a nova versão do arquivo ZIP. Executado como usuário grid e oracle.

### Limpar os patches antigos
```bash
opatch util DeleteInactivePatches -silent
```

### Descompactar o PSU V_01
```bash
cd /install/oracle/psu/janeiro/
unzip p37262208_190000_Linux-x86-64.zip
```
Devido a ausência de espaço na partição /install na maioria dos servidores será necessário uma solução de contorno, segue a versão 2 para caso falte espaço

### Descompactar o PSU V_02
```bash
cd /install/oracle/psu/janeiro/
mv p37262208_190000_Linux-x86-64.zip /u01/app/oracle/diag
unzip /u01/app/oracle/diag/p37262208_190000_Linux-x86-64.zip -d /install/oracle/psu/janeiro/
```

## Aplicação do PSU (19c)
### Variáveis de ambiente (Logar no root)

```bash
sudo su -
export GRID_HOME=$(grep -i "+asm" /etc/oratab | cut -d ':' -f 2)
export OCW=37268031
export ACFS=37266638
export DBWLM=36758186
export DBRU=37260974
export TOMCAT=37461387
export OJVM=37102264
export INSTALL_19C=/install/oracle/psu/janeiro/37262208/37257886
```
Define as variáveis de ambiente, incluindo GRID_HOME e os números dos patches para cada componente. Executado como root.

### Pré-aplicação (Cluster - CRS)

```bash
$GRID_HOME/crs/install/rootcrs.sh -prepatch
```
Executa scripts de pré-aplicação para ambientes Cluster.

### Pré-aplicação (Single Instance - HAS)
```bash
$GRID_HOME/crs/install/roothas.sh -prepatch
```
Executa scripts de pré-aplicação para Single Instance.

### Aplicação dos patches no grid
Exportação das variáveis
```bash
su - grid
export GRID_HOME=$(grep -i "+asm" /etc/oratab | cut -d ':' -f 2)
export OCW=37268031
export ACFS=37266638
export DBWLM=36758186
export DBRU=37260974
export TOMCAT=37461387
export OJVM=37102264
export INSTALL_19C=/install/oracle/psu/janeiro/37262208/37257886
```

Aplicação dos patches
```bash
$GRID_HOME/OPatch/opatch apply -local -silent $INSTALL_19C/$OCW
```
```bash
$GRID_HOME/OPatch/opatch apply -local -silent $INSTALL_19C/$ACFS
```
```bash
$GRID_HOME/OPatch/opatch apply -local -silent $INSTALL_19C/$DBWLM
```
```bash
$GRID_HOME/OPatch/opatch apply -local -silent $INSTALL_19C/$DBRU
```
```bash
$GRID_HOME/OPatch/opatch apply -local -silent $INSTALL_19C/$TOMCAT
```
```bash
exit
```

### Aplicação dos patches no oracle
```bash
su - oracle
export GRID_HOME=$(grep -i "+asm" /etc/oratab | cut -d ':' -f 2)
export OCW=37268031
export ACFS=37266638
export DBWLM=36758186
export DBRU=37260974
export TOMCAT=37461387
export OJVM=37102264
export INSTALL_19C=/install/oracle/psu/janeiro/37262208/37257886
```

Aplicacao dos patches
```bash
$INSTALL_19C/$OCW/custom/scripts/prepatch.sh
```
```bash
$ORACLE_HOME/OPatch/opatch apply -local -silent $INSTALL_19C/$OCW
```
```bash
$ORACLE_HOME/OPatch/opatch apply -local -silent $INSTALL_19C/$DBRU
```
```bash
$INSTALL_19C/$OCW/custom/scripts/postpatch.sh
```

### Aplicação do patch OJVM(Usuário oracle)
```bash
cd /install/oracle/psu/janeiro/37262208/$OJVM
opatch apply -local -silent
```

### Pós-aplicação (Cluster - CRS)
```bash
sudo su -
export GRID_HOME=$(grep -i "+asm" /etc/oratab | cut -d ':' -f 2)
$GRID_HOME/rdbms/install/rootadd_rdbms.sh
```
```bash
$GRID_HOME/crs/install/rootcrs.sh -postpatch
```
Executa scripts de pós-aplicação para ambientes Cluster.

### Pós-aplicação (Single Instance - HAS)
```bash
sudo su -
export GRID_HOME=$(grep -i "+asm" /etc/oratab | cut -d ':' -f 2)
$GRID_HOME/rdbms/install/rootadd_rdbms.sh
```
```bash
$GRID_HOME/crs/install/roothas.sh -postpatch
```
Executa scripts de pós-aplicação para Single Instance.

### Datapatch
```bash
su - oracle
cd $ORACLE_HOME/OPatch
./datapatch -verbose
```

### Validação
```sql
SELECT * FROM dba_registry_sqlpatch ORDER BY action_time DESC ;
```

### Limpeza
```bash
sudo su -
```
```bash
su - grid
```
```bash
cd /install/oracle/psu/janeiro/
```
```bash
rm -rf *
```
Remove os arquivos do PSU após a aplicação. Executado como grid.
