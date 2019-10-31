<p><strong>&nbsp;</strong></p>
<p>Contents</p>
<ol>
<li><a href="#_Toc471829370"> Introduction. 2</a></li>
<li><a href="#_Toc471829371"> Scope. 2</a></li>
<li><a href="#_Toc471829372"> Intendant audience. 2</a></li>
<li><a href="#_Toc471829373"> Point to be considered/Further research. 2</a></li>
<li><a href="#_Toc471829374"> High Level diagram.. 3</a></li>
<li><a href="#_Toc471829375"> Used tools. 4</a></li>
<li><a href="#_Toc471829376"> Configuration and setup. 4</a></li>
</ol>
<p><a href="#_Toc471829377">7.1 Asynchronous Communication between Master/Slave. 4</a></p>
<p><a href="#_Toc471829378">7.2 Percona XtraDB Cluster Configurations: 4</a></p>
<p><a href="#_Toc471829379">7.3 Master/Slave configuration for Asynchronous DB replication. 5</a></p>
<p><a href="#_Toc471829380">STEP 1: Make a backup on TheMaster and prepare it. 5</a></p>
<p><a href="#_Toc471829381">STEP 2: Copy backed up data to TheSlave. 7</a></p>
<p><a href="#_Toc471829382">STEP 3: Configure The Master&rsquo;s MySQL server. 7</a></p>
<p><a href="#_Toc471829383">STEP 4: Start the replication. 8</a></p>
<p><a href="#_Toc471829384">STEP 5: Check the Master/Slave status. 8</a></p>
<ol start="8">
<li><a href="#_Toc471829385"> References. 8</a></li>
</ol>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p><strong>&nbsp;</strong></p>
<h1><a name="_Toc471829370"></a>1.&nbsp;&nbsp;&nbsp;&nbsp; Introduction</h1>
<p>This document is giving high level configuration about Percona 5.7, 3 node cluster with Master/Slave database Replication for Disaster Recovery (DR) between Production Data Center (DC) and Disaster Recovery Data Center.</p>
<h1><a name="_Toc471829371"></a>2.&nbsp;&nbsp;&nbsp;&nbsp; Scope</h1>
<p>This document covers about to configure Master/Save Database (DB) replication in centos using Percona technologies.</p>
<h1><a name="_Toc471829372"></a>3.&nbsp;&nbsp;&nbsp;&nbsp; Intendant audience</h1>
<p>All configuration and setup are being done on Centos&nbsp; 6, Linux platform. So Database Administrator or System Administrator or Developer should have basic knowledge of Linux command, MySQL database, and Master /slave information to follow this document.</p>
<h1><a name="_Toc471829373"></a>4.&nbsp;&nbsp;&nbsp;&nbsp; Point to be considered/Further research</h1>
<p>Before start doing DB replication configuration, there may be following points to be considered.</p>
<ul>
<li>Network stability between Production Datacenter and DR Datacenter</li>
<li>MySQL version at both Production DB and DR DB</li>
<li>DB replication strategy for MySQL storage ENGINE. Like InnoDB, MyISAM</li>
<li>Running query by Application.</li>
</ul>
<p>&nbsp;</p>
<h1><a name="_Toc471829374"></a>5.&nbsp;&nbsp;&nbsp;&nbsp; High Level diagram</h1>
 <img src="Percona_Cluster_ 5_7_Master_Slave_Replication.png" alt="High Level Diagram" height="761px" width="848px"> 
<p>This high level digram is showing two Application sets. i.e Production Set at Production Dataceter and DR Set at Disaster Recovery Datacenter. Based on DR platform configuration we need to create identical sets of components between Production and DR platform. For this, web server/Application server, Load Balancer for both App &amp; DB, and DB Cluster are the identified components for platform setup. Running application does not often change compare to DB. So we are focusing on DB synchronization between Production and DR platform. We cannot put synchronous communication medium to synch DB due to network latency, each transaction cycle in DB. Due to this, we need to use Asynchronous mechanism for DB synch.</p>
<p>In above figure, Red highlighted rectangle is detonated as the Virtual Private Network (VPN) between Production DC and DR DC. <strong>Perconca</strong> tools to make Asynchronous DB replication/Hot Backup between Production DB cluster and DR DB cluster. There is used Master/Slave concept for db replication as source and destination DB server. One of the node from Production 3 node cluster as Master and One of the node from DR Node Cluster.</p>
<h1><a name="_Toc471829375"></a>6.&nbsp;&nbsp;&nbsp;&nbsp; Used tools &nbsp;</h1>
<ul>
<li>Percona 5.7 cluster</li>
<li>Innobackupex/ xtrabackup</li>
<li>Yum</li>
</ul>
<h1><a name="_Toc471829376"></a>7.&nbsp;&nbsp;&nbsp;&nbsp; Configuration and setup</h1>
<h2><a name="_Toc471829377"></a>7.1 Asynchronous Communication between Master/Slave</h2>
<p>Asynchronous Replication uses the approach of lazy replication. The master database asynchronously propagates replica updates to other slave nodes. <strong>Percona</strong> comes with a tool that allows you to perform Asynchronous DB replication/Hot Backup s of a running MySQL database, with no blocking. It&rsquo;s able to do this because of Innodb &amp; multi-version concurrency control (MVCC). Luckily we don&rsquo;t need to dig into the guts to enjoy the benefits of this great technology.</p>
<h2>&nbsp;<a name="_Toc471829378"></a>7.2 Percona XtraDB Cluster Configurations:</h2>
<p>Percona is an open source software company specializing in MySQL, MongoDB, and other open source database support, consulting, managed services, and training.</p>
<p>Percona XtraDB Cluster is an active/active high availability and high scalability open source solution for MySQL clustering. It integrates Percona Server and Percona XtraBackup with the Codership Galera library of MySQL high availability solutions in a single package that enables you to create a cost-effective MySQL high availability cluster.</p>
<p>We can found more information</p>
<p><a href="https://www.percona.com/software/mysql-database/percona-xtradb-cluster">https://www.percona.com/software/mysql-database/percona-xtradb-cluster</a></p>
<p><strong>Installation Documentation</strong>: <a href="https://www.percona.com/downloads/Percona-XtraDB-Cluster-57/LATEST/">https://www.percona.com/downloads/Percona-XtraDB-Cluster-57/LATEST/</a></p>
<p>In other document already described about how to configure Percona cluster 5.7 cluster with High Availability. This document consists about configuration about DB replication between two sets of db cluster. Each Cluster consists 3 nodes because 3 nodes clusters is best practice for clustering. DB replication configuration step starts once all Cluster successfully configuration at both Production DC and DR DC. Then we will pick one node as Master form Production DC and one node as from DR DC</p>
<p><em>&nbsp;</em></p>
<p>&nbsp;</p>
<p><strong>&nbsp;</strong></p>
<h2><a name="_Toc471829379"></a>7.3 Master/Slave configuration for Asynchronous DB replication</h2>
<p>Based on High level diagram, configuration will be as follows:<br /> -<strong> TheMaster</strong> ,Node at Production Cluster-<strong>Node3 (192.168.2.6)<br /> </strong>-<strong> TheSlave</strong> ,Node at DR Cluster &ndash; <strong>Node 1 (192.168.3.4)</strong></p>
<p><strong>TheMaster</strong>, A system with a MySQL-based server installed, configured and running. This system will be called TheMaster, as it is where your data is stored and the one to be replicated. We will assume the following about this system:</p>
<ul>
<li>the MySQL server is able to communicate with others by the standard TCP/IP port;</li>
<li>the SSH server is installed and configured;</li>
<li>you have a user account in the system with the appropriate permissions;</li>
<li>you have a MySQL&rsquo;s user account with appropriate privileges.</li>
<li>server has binlogs enabled and unique server-id set up.</li>
</ul>
<p><strong>TheSlave</strong> Another system, with a MySQL-based server installed on it. We will refer to this machine as <strong>TheSlave</strong> and we will assume the same things we did about <strong>TheMaster</strong>, except that the server-id on <strong>TheSlave</strong> .</p>
<p><strong>Percona XtraBackup</strong>, The backup tool we will use. It should be installed in both computers for convenience.</p>
<h3><a name="_Toc471829380"></a>STEP 1: Make a backup on TheMaster and prepare it</h3>
<p><strong>Binary logs on PXC<br /> </strong>Binary logs are not strictly needed in PXC for replication, but you may be using them for backups or for asynchronous slaves of the cluster.&nbsp; To set them up properly, we need the following settings added to our config <strong>. </strong>For this, following configuration needs to be added in <strong>TheMaster</strong>, my.cnf config file.</p>
<p><em>&nbsp;<strong># In order for Galera to work correctly binlog format should be ROW</strong><br /> binlog_format=ROW<br /> <strong>#For DB replication</strong><br /> server_id=19216826<br /> #binlog_format=ROW<br /> log_slave_updates=1<br /> log_bin=binlog</em></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>Sample <strong>TheMaster, My.cnf</strong> will be ,After adding Replication parameter<br /> <em>[mysqld]<br /> datadir=/var/lib/mysql<br /> user=mysql<br /> #log-error=/var/log/mysqld.log<br /> # Path to Galera library<br /> wsrep_provider=/usr/lib64/libgalera_smm.so<br /> # Cluster connection URL contains the IPs of node#1, node#2 and node#3wsrep_cluster_address=gcomm://192.168.2.4,192.168.2.5,192.168.2.6<br /> # In order for Galera to work correctly binlog format should be ROW<br /> binlog_format=ROW<br /> <strong>#For DB replication<br /> server_id=19216826<br /> #binlog_format=ROW<br /> log_slave_updates=1<br /> log_bin=binlog<br /> #gtid_mode=ON</strong><br /> #enforce_gtid_consistency=1<br /> # MyISAM storage engine has only experimental support<br /> default_storage_engine=InnoDB<br /> # This InnoDB autoincrement locking mode is a requirement for Galera<br /> innodb_autoinc_lock_mode=2<br /> # Node 1 address<br /> wsrep_node_address=192.168.2.6<br /> # SST method<br /> wsrep_sst_method=xtrabackup-v2<br /> # Cluster name<br /> wsrep_cluster_name=test_5_7cluster_node<br /> # Authentication for SST method<br /> wsrep_sst_auth="sstuser:s3cret"</em></p>
<p><strong>TO get snapshot from Master DB:<br /> </strong><em>TheMaster # innobackupex &nbsp;--host=127.0.0.1 --user=bkpuser&nbsp; --password=bkppassword --port=3306 &nbsp;/data/backups/new_backup/ </em></p>
<p>After this is finished you should get :<br /> <strong><em>innobackupex: completed OK! </em></strong></p>
<p>This will make a copy of your MySQL data dir to the <strong><em>/data/backups/new_backup/</em>$TIMESTAMP</strong>. You have told Percona XtraBackup (through the <strong>innobackupex</strong> script) to connect to the database server using your database user and password, and do a hot backup of all your data in it (all MyISAM, InnoDB tables and indexes in them). <br /> In order for snapshot to be consistent you need to prepare the data:<br /> <em>TheMaster # innobackupex --apply-log --host=127.0.0.1 --user=bkpuser&nbsp; --password=bkppassword /data/backups/new_backup/</em>$TIMESTAMP <br /> </p>
<h3><a name="_Toc471829381"></a>STEP 2: Copy backed up data to TheSlave<br /> </h3>
<ul>
<li><em>TheMaster # scp -r /data/backups/new_backup/</em>$TIMESTAMP<em> root@</em> <em>TheSlave:/data/</em></li>
<li>Stop mysql at slave after successfully copied file from Master</li>
<li>At minimum you need to set the server_id to a unique value. The IP address with the periods removed can make a good server_id. Like 19216834</li>
<li>Update mysql data directory with just copied from Master.<br /> <em>TheSlave$ mv /path/to/mysql/datadir /path/to/mysql/datadir_bak<br /> </em>and move the snapshot from TheMaster in its place:<em><br /> TheSlave$ mv /path/to/mysql/$TIMESTAMP /path/to/mysql/datadir<br /> </em>After you copy data over, make sure MySQL has proper permissions to access them.<em><br /> TheSlave$ chown mysql:mysql /path/to/mysql/datadir<br /> </em>In case the ibdata and iblog files are located in different directories outside of the datadir, you will have to put them in their proper place after the logs have been applied</li>
</ul>
<h3><a name="_Toc471829382"></a>STEP 3: Configure The Master&rsquo;s MySQL server</h3>
<p>Add the appropriate grant in order for slave to be able to connect to master:<br /> <em>TheMaster|mysql&gt; GRANT REPLICATION SLAVE ON *.*&nbsp; TO 'repl'@'$slaveip' IDENTIFIED BY '$slavepass';</em></p>
<p>Also make sure that firewall rules are correct and that TheSlave can connect to TheMaster. Test that you can run the mysql client on TheSlave, connect to TheMaster, and authenticate.<br /> TheSlave$ mysql --host=TheMaster --user=repl --password=$slavepass</p>
<p>Verify the privileges.<br /> <em>mysql&gt; SHOW GRANTS;</em></p>
<p><em><br /> </em></p>
<p><em>&nbsp;</em></p>
<h3><a name="_Toc471829383"></a>STEP 4: Start the replication</h3>
<ul>
<li>Start/restat MySQL on TheSlave</li>
<li>Look at the content of the file xtrabackup_binlog_info, it will be something like:<br /> TheSlave$ cat /var/lib/mysql/xtrabackup_binlog_info<br /> TheMaster-bin.000001 481</li>
<li>Execute the <strong>CHANGE MASTER</strong> statement on a MySQL console and use the username and password you&rsquo;ve set up in <strong>STEP 3</strong>:</li>
</ul>
<p>TheSlave|mysql&gt; CHANGE MASTER TO<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; MASTER_HOST='$masterip',<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; MASTER_USER='repl',<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; MASTER_PASSWORD='$slavepass',<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; MASTER_LOG_FILE='TheMaster-bin.000001',<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; MASTER_LOG_POS=481;</p>
<ul>
<li>and start the slave:<br /> TheSlave|mysql&gt; START SLAVE;</li>
</ul>
<h3><a name="_Toc471829384"></a>STEP 5: Check the Master/Slave status</h3>
<p>You should check that everything went OK with:<br /> <em>TheSlave|mysql&gt; SHOW SLAVE STATUS \G<br /> </em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ...<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Slave_IO_Running: Yes<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Slave_SQL_Running: Yes<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ...<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Seconds_Behind_Master: 13<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ...<br /> <br /> </p>
<h1><a name="_Toc471829385"></a>8.&nbsp;&nbsp;&nbsp;&nbsp; References</h1>
<p><a href="https://www.percona.com/blog/2015/07/16/bypassing-sst-pxc-binary-logs/">https://www.percona.com/blog/2015/07/16/bypassing-sst-pxc-binary-logs/</a><br /> <a href="https://www.percona.com/doc/percona-xtrabackup/2.1/howtos/setting_up_replication.html">https://www.percona.com/doc/percona-xtrabackup/2.1/howtos/setting_up_replication.html</a><br /> <a href="https://www.handybackup.net/mysql-replication.shtml">https://www.handybackup.net/mysql-replication.shtml</a><br /> <a href="http://www.iheavy.com/2012/04/17/easy-mysql-replication-with-hotbackups-2/">http://www.iheavy.com/2012/04/17/easy-mysql-replication-with-hotbackups-2/</a></p>
https://www.howtoforge.com/setting-up-a-high-availability-load-balancer-with-haproxy-keepalived-on-debian-lenny
