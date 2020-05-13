# How to upgrade Windows Server OS on cluster nodes
This article shows how to UPGRADE (not update) Windows Server OS on cluster nodes with 2 nodes mirror disk type cluster.

## Assumption
- EXPRESSCLUSTER X for Windows version: 4.0 or later
- 2 nodes (Primary and Secondary) with Mirror Disk cluster

## Note
- When you upgrade Windows OS version (e.g. from Windows Server 2012 to 2016, from 2016 to 2019 ..), EXPRESSCLUSTER is required to be uninstalled.
- While uninstallin EXPRESSCLUSTER and upgrading Windows Server OS, failover group can run on another server. However, while applying cluster configuration after upgrading Windows Server OS, failover group is requred to stop and start.

## Preparation
1. Confirm folowings:
	- Failover group is active on Primary Server.
	- No errors/warnings on the cluster.
	- Mirror Disk is synchronized.

## Procedure
- On Secondary Server
	1. Stop cluster service on the server.
		```bat
		> clpcl -t
		```
	1. Change EXPRESSCLUSTER services startup type from auto to MANUAL.
		- For X4.0 and 4.1:
			- EXPRESSCLUSTER
			- EXPRESSCLUSTER Event
			- EXPRESSCLUSTER Manager
			- EXPRESSCLUSTER Old API Support
			- EXPRESSCLUSTER Server
			- EXPRESSCLUSTER Transaction
			- EXPRESSCLUSTER Web Alert
		- Reference:
			[Batch file to change EXPRESSCLUSTER services startup type](https://github.com/EXPRESSCLUSTER/Tools/blob/master/disableServiceAutoStart.bat)
	1. Reboot the server.
	1. Confirm that all EXPRESSCLUSTER services are NOT running.
	1. Uninstall EXPRESSCLUSTER.
	1. Reboot the server.
	1. Upgrade Windows Server OS.
	1. Install EXPRESSCLUSTER Server and register licenses.
		- *Note*
			- Install the same version EXPRESSCLUSTER as Primary Server.
	1. Reboot the server.
	1. Confirm followings are NOT changed:
		- Hostname
		- IP addresses for Interconnect setting
 		- Drive Letters for md resources
	1. Confirm 
- On Primary Server
	1. Start Cluster WebUI.
		- Failover group should be active on Primary Server.
		- Secondary Server should be Offline.
	1. Stop Failover gourp.
		- **Note**
			- At this time, failover group should be de-activated until applying cluster configuraton.
	1. Go to Cluster WebUI Config Mode.
	1. Apply the configuration.
		- **Note**
			- To apply the cluster configuration to Secondary Server and GUID of Secondary Server Mirror Disk to cluster configruation, applying is required.
	1. Confirm that Secondary Server gets Online.
	1. Activate failover group on Primary Server.
	1. Confirm that FULL RECOVERY (not Fast Recovery) runs from Primary Server to Secondary Server.
		- If it does not run automatically, execute FULL RECOVERY manually.
	1. After Full Recovery s completed, move failover group to Secondary Server and upgrade Primary Server Windows Server OS as the above.
