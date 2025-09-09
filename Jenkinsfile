pipeline {
    agent any

    stages {

        stage('Check /u01 and /u01/dmp Space') {
            steps {
                sshagent(credentials: ['credentials-id']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no user@ip "
                            echo '--- /u01 space usage ---'
                            df -h /u01

                            echo '--- /u01/dmp size ---'
                            du -sh /u01/dmp 2>/dev/null || true
                        "
                    '''
                }
            }
        }

        stage('Check Oracle Tablespaces') {
            steps {
                sshagent(credentials: ['credentials-id']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no user@ip "
                            export ORACLE_HOME=/Path/To/dbhome_1
                            export PATH=\\$ORACLE_HOME/bin:\\$PATH
                            export LD_LIBRARY_PATH=\\$ORACLE_HOME/lib:\\$LD_LIBRARY_PATH

                            sqlplus -s sys/Password@ip:port/serviceName as sysdba <<EOF
                            SET HEADING ON
                            SET FEEDBACK OFF
                            SET LINESIZE 200
                            SET PAGESIZE 100
                            COL TABLESPACE_NAME FOR A20
                            COL USED_MB FOR 999999.99
                            COL SIZE_MB FOR 999999.99
                            COL FREE_MB FOR 999999.99
                            SELECT TABLESPACE_NAME,
                                   ROUND((USED_SPACE*8)/1024,2) USED_MB,
                                   ROUND((TABLESPACE_SIZE*8)/1024,2) SIZE_MB,
                                   ROUND(((TABLESPACE_SIZE-USED_SPACE)*8)/1024,2) FREE_MB
                            FROM DBA_TABLESPACE_USAGE_METRICS;
                            EXIT;
EOF
                        "
                    '''
                }
            }
        }

        stage('Check Oracle User Password Expiry') {
            steps {
                sshagent(credentials: ['credentials-id']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no user@ip "
                            export ORACLE_HOME=/Path/To/dbhome_1
                            export PATH=\\$ORACLE_HOME/bin:\\$PATH
                            export LD_LIBRARY_PATH=\\$ORACLE_HOME/lib:\\$LD_LIBRARY_PATH

                            sqlplus -s sys/password@ip:port/serviceName as sysdba <<EOF
                            SET HEADING ON
                            SET FEEDBACK OFF
                            SET LINESIZE 200
                            COL USERNAME FOR A20
                            COL EXPIRY_DATE FOR A30
                            SELECT USERNAME, ACCOUNT_STATUS, EXPIRY_DATE
                            FROM DBA_USERS
                            WHERE USERNAME IN ('APEX_PUBLIC_USER','ANONYMOUS','ORDS_PUBLIC_USER');
                            EXIT;
EOF
                        "
                    '''
                }
            }
        }

    }
}
