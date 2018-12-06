Sometimes, users see a lot of "broken pipe/connection drop" errors when they try to access their instances. This is because of a bug in production which will get fixed after April 2016. For now, the solution is to have both Router's gateway and floating-ip's in one subnet. To see if you are affected by this bug or not, perform the following steps:-

* Login to horizon and goto Networks -> Routers -> (click on router-name)
* Check the Gateway address of the Router. If the ip-address is in 129.10.3.0/25 subnet, then make sure the floating-ip's you have are from the same subnet only (129.10.3.0/25 and not 129.10.3.128/26). If your router is in subnet 129.10.3.128/26, your floating-ip's should also be in the same subnet.

![floating-ips](https://raw.githubusercontent.com/wiki/CCI-MOC/moc-public/tutorial_screenshots/kilo/floatingips.png)
![router gateway](https://raw.githubusercontent.com/wiki/CCI-MOC/moc-public/tutorial_screenshots/kilo/router.png)

If there is a discrepancy, then you can try clearing the gateway of router and setting it again, or can try deleting the router and creating it again or can try freeing the wrong floating-ip and allocating it again. In the end, you should have your router and floating-ip's in the same subnet, either subnet1 or subnet2 but not both.

If you are still facing issues or are unable to work with the above mentioned steps, you can reach to us at moc-kaizen-l@bu.edu for help.
