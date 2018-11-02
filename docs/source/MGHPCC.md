* [Inventory](#inventory)
* [Storage Cage](#storage-cage)
* [Keys and Access](#keys-and-access)
* [[PDU Documentation]] (link is to another wiki page)

## Where stuff is

Server locations at MGHPCC are described by **row**, **pod**, **cabinet**, and **u**.  Racks can be referred to with an abbreviated version of this, so the rack at Row 5, pod A, cabinet 2 would be **r5pAc2**.

Cluster locations:   

Cluster | University | Location | Access 
---- | ---- | ---- | ----
Kaizen | NEU | r3pB c15, c17 & c19 | Must ask NEU to unlock for access
Kumo | BU | r4pA c23 | MOC key opens these
Engage1 | BU | r4pA c01 & c02 | MOC key opens these
Engage1 - Brocade | MIT | r5pA c02-c11, c13-17, c19, c21, c23  | Must ask MIT to unlock for access
Engage1 - Cache Servers | MIT | r5pA c07, c08, c09, c10, c13 | Must ask MIT to unlock for access

* there is also an MIT-owned Dell switch in r5pAc01 we manage



***

## Inventory 

Physical inventory of what we have at MGHPCC is maintained at this Google Sheet, please keep it up to date:   

[MGHPCC Inventory - Physical Hardware](https://docs.google.com/spreadsheets/d/1oim9TuwZ_6EHqR0AGcwCkUlrfzrVh3L71GbaM9-RM70/)

If these links aren't working for you, the sheets are owned by mocwebsiteacct@gmail.com and are located in the `Infrastructure Documents --> MGHPCC Inventory` folder of that account's Google Drive.  Depending on your needs, ask Piyanai or someone on the Infrastructure team to share the folder or doc with you.

***

## Storage Cage

We are permitted to store things in the red MOC crate BU storage cage in the "IT Staging" area.  This crate is labeled "Property of Massachusetts Open Cloud".  (The labels should actually be updated from kaizen@lists.massopen.cloud to infra@lists.massopen.cloud.)

The key to the storage area is on the BU_MOC keyring along with the Pod A key.

It's OK to store an additional box or two in there as long as it's clearly labeled, but in general don't store random crap that you won't need in there.  Don't take anything from the storage cage which does not belong to MOC.

### Inventory of the Red Crate

#### Tools
- tape measure
- sharpie
- flat metal tool for removing rack nuts 
- Trendnet Serial to USB adapter
- blue console cable (serial to RJ45)
- plug adapter 3-prong to C14 (useful for plugging laptop in at PDU)
- Velcro roll
- bag of possibly useful static bags, plastic bags, etc.
- misc. rack nuts/screws

#### Cables/Networking parts

###### 10G cables
- (1) spare Brocade 3m 10G SFP-SFP cable
- (3) spare Tripp Lite 2m 10G SFP-SFP cables (N280-02M-BK)
- (2) spare 15m LC-LC fiber (aqua)

###### SFP transceivers
- (3) spare Tripp Lite Cisco-compatible (N286-10GSR-MDLC)
- (1) 10-pack of generic transceivers (picked up 6/28/2017 from receiving) for CC
SAIL connections

###### gigabit ethernet cables (RJ45)
- (1) 3 foot blue ethernet
- (2) 7 foot blue ethernet
- (1) 3 foot gray ethernet
- (1) 7 foot gray ethernet
- (1) 50 foot ethernet cable (green)
- (3) 14 foot ethernet (purple)
- (2) 9 foot ethernet (assorted)

#### Switch & Server Parts
- (3) 4-point mount rail kits for Cisco Catalyst management switches (Engage1 ann
d Kumo) - these need to be installed
- (3) spare SystemX HDD - spares
- (1) spare Nic from Quanta Server - spare
- (1) spare SSD disk tray caddy (type used for SSDs in Engage1 Lenovo Storage)

***

## Keys & Access

MOC staff who have badges for the datacenter should have access to the BU_MOC key.  Currently this is limited to Rado, Austin, and Piyanai.  

If someone needs to be given temporary access to the key, such as a warranty repair tech, send an email to Manny Ruiz (maruiz@bu.edu) with the person's name and the dates they need access.  Make sure to CC both Piyanai (piyanai@bu.edu) and Mike Dugan (dugan@bu.edu) on the request.

You can also ask Manny for help adding a new employee who needs a badge.  If someone (such as an intern) who does not normally need access is coming with you to help out, you can request temporary access for them to the "IT Access" key fob which will allow them to move around the building, but does not include any keys.






