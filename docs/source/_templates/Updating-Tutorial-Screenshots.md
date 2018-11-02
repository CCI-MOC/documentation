### Organization of the image files
Images are organized in folders by OpenStack release.  If you are updating an image, give the new file the same name as the old one, but place it in the folder corresponding to the newer release.     

### Deciding which images to update
Each time a new OpenStack release is deployed in Kaizen, check through the images.  Only update images where the screen is substantially different. (Note: Some of the oldest images may still be links in the format `<img src="<some imgur url>">`.  These images should always be updated.)

Examples:

1. From Kilo to Liberty, the login page looks almost exactly the same, but the blue button text changed from "Sign In" to "Connect".  Our users are smart enough to figure this out, no need to update this image.
2. From Kilo to Liberty, the network diagrams on the Network Topology page look very different. Networks used to be represented as vertical bars, now they are represented as floating clouds.  These images need to be updated.

### How to update the images

1. Add yourself with the role '_member_' to the project 'tutorial_project'. (If you are not an admin user, get an admin to add you).  It's important to be _member_ on the project, otherwise the screenshots will show admin-only options, which would be confusing to the end users.
1. Log out and back in so the project is available to you, and switch to the project.
1. The project should have been left in a clean state, but if there are still resources attached to it, delete all of them.  
1. Follow every step in the tutorial, using the same names for instances, networks, etc. as in the instructions.  
1. Compare your screen to each screenshot as you go, and take new screenshots when needed. 
1. You should also update any tutorial text that is out of date.  
1. Crop and appropriately resize your screenshot images (see [Criteria for images](#criteria-for-images) below)
1. Add the image to the subdirectory for the relevant OpenStack release. Give the image file the same name as the old one.
1. Update the image source path.  For example, `tutorial_screenshots/kilo/network_topology_01.png` would become `tutorial_screenshots/libety/network_topology_01.png`. Make sure to do this for every reference to the image - some are referenced on multiple pages. To find all references to the image 'network_topology_01.png' you can run `grep "network_topology_01.png" ./* inside the moc-public.wiki folder`.
1. Commit your changes and push the commit to github.
1. Check through the tutorial again with your browser to make sure your new images appear, and that all images load properly.  
1. If you are done with the updates, delete all of the resources in the tutorial_project, release floating IPs, etc.

### Criteria for images
* Images of the full screen should be about 900 pixels wide.  Images of smaller popups should be about 575 pixels wide.
* Crop browser tabs, desktop backgrounds, etc. out of the images so that only the OpenStack dashboard is showing.  This helps the images look as uniform as possible. 
* No admin-only options should be visible.  Make sure you have the _member_ role but not the admin role on tutorial_project.
* Try to be as consistent as possible with the information already in the tutorial - give things like networks, public keys, etc. the same name as they have in the old photos.
* Images should match what is described in the surrounding text. If the text is incorrect, update it.
* Make sure the images are easy to see once on the wiki page.  Because of the way Horizon stretches itself to fill the screen, you may need to make your browser window as narrow as you can without obscuring anything, in order to make the page readable in the screenshot. It's a good idea to do just one screenshot, add it to the wiki, and make sure it looks OK before you continue.
