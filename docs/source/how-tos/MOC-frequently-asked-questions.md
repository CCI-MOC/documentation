## MOC FAQs



<details>
	<summary>
		1) Every time I spin up a new instance of ubuntu I am unable to connect to the internet without editing
		/etc/resolv.config and setting nameservers 8.8.8.8, 8.8.4.4.</summary>
	<br>
	Put the DNS nameservers as specified in below links when creating/updating the private-network.
	<br>
	<br>
</details>


[Creating/updating private network](../openstack/Set-up-a-Private-Network.html)

![](../_static/img/create_network_details.png)


<details>
	<summary>
		2) Unable to ssh/ping to my instance from outside world even though I have floating-ip assigned to
		it</summary>
	<br>
	Edit security-groups and allow ssh/ping to your instance. Unless you do that, you can’t access those ports on your
	instance from outside.
</details>
<details>
	<summary>
		3) MGHPCC Shutdown </summary>
	<br>
	Before getting started
	Double check with Norhteastern and BU if they are planning any network upgrades around the shutdown period.
	<details>
		<summary>
			Turning off Kumo </summary>
		<br>
		1. Turn off all dell blades from the chassis management controller (easy).
		<br>
		2. Next step, turn off all VMs on Kumo Storage.
		<br>
		3. Turn off Kumo storage itself.
		<br>
		4. Turn off Ceph
		Logon to kumoservices and then turn off kumo-ceph0{1-3}
		<br>
		5. Turn off VMs on kumo services (at least kumo-cephmanager is running on it)
		<br>
		6. Logon to kumo emergency (kumo-e.massopen.cloud) and turn off all the VMs there
		<br>
		7. Let kumo services and emergency box be on.
		<br>
	</details>
	<details>
		<summary>
			Turning off Kaizez
		</summary>
		<br>
		1. Power off all elastic hosts (send it ipmitool chassis power soft)
		<br>
		2. Shutdown HIL, BMIm and vms on the kznbmihost1,2
		<br>
		3. Power off all the openstack VMs (in both Kaizens; we probably won’t have old kaizen next year)
		<br>
		4. Then power off the computes, controllers.
		<br>
	</details>
	<details>
		<summary>
			Turning off Engage1
		</summary>
		<br>
		1. Power off openstack VMs, then computes and then controllers.
		<br>
		2. Power off Ceph.
		<br>
		3. Power off VMs on engage1-services and emergency.
		<br>
	</details>
	For oVirt VMs, Simply poweroff the VMs
</details>