See some other blueprints at: https://blueprints.launchpad.net/openstack

Name: nova-qualified-uuids

Blueprint

# Description

OpenStack in general, and Nova specifically, makes the assumption that there is only one endpoint for each type of service. This blueprint outlines some use cases on why supporting multiple endpoints is a desirable feature and proposes some solutions. Fundamentally, the problem is that OpenStack refers to objects (e.g., VMs, volumes and networks) using UUIDs. When there is only a single nova, cinder or neutron, this is fine since each object type is served from only a single endpoint. When there are multiple instances of each of these services with multiple endpoints, however, it is undefined which endpoint owns a particular UUID.

# Use Cases
There are multiple scenarios where different OpenStack services may be stood up under different administrative domains, where the consumer would like to mix and match between them.  For example:
* In a company with an existing OpenStack install, a different department stands up a new service. Users then want to access glance or cinder services from the first service while using Nova from the second service
* In a shared university environment where some projects span departments or institutions in the same data center due to grant funding 
* The [Massachusetts Open Cloud](http://massopencloud.org)

A more specific example would be a customer who has purchased 2 VMs from Nova Provider A that are kept in the same project. That same customer has also purchased 2 block volumes: one from (cheap) Provider B and another on the higher quality Provider C. The customer wants 1 VM to be on the cinder instance maintained by Provider B and the other on Provider C. Currently, there is no way to identify which of the two cinder providers a object is coming from given just the objects UUID.

# Specific Interactions
This section outlines specific interactions and code bases that make the assumption of one endpoint equating to one service type.

## Nova -> Cinder
As currently implemented, Nova, via the _cinderclient()_ function in nova/volume/cinder.py, assumes that for all volumes with which it works (with the exception of regions) there is only a single cinder endpoint:
```python
if CONF.cinder_endpoint_template:
    url = CONF.cinder_endpoint_template % context.to_dict()
else:
    if CONF.os_region_name:
        attr = 'region'
        filter_value = CONF.os_region_name
    else:
        attr = None
        filter_value = None
    url = sc.url_for(attr=attr,
                     filter_value=filter_value,
                     service_type=service_type,
                     service_name=service_name,
                     endpoint_type=endpoint_type)
```

This will need to change in order to support multiple providers of cinder block storage services.

# Proposed solutions

## Augmented UUIDs (preferred)
One solution that would preserve much of the existing logic in OpenStack (including the REST API) would be to take advantage of the string-based storage of UUIDs in order to include an endpoint identifier. This would transform UUIDs from being opaque string identifiers into parsed strings, which may or may not be desirable.

This would transform a UUID from looking like this:

    045a32e7-7eab-4b87-bc87-d7bd148f4558
to:

    https://cinder.provider.com:2600/v2;045a32e7-7eab-4b87-bc87-d7bd148f4558
or:

    045a32e7-7eab-4b87-bc87-d7bd148f4558@https://cinder.provider.com:2600/v2

There may be an additional database storage cost associated with this, as IDs are currently stored as varchar(36).

Though minimal, several pieces of code at the intersections of services that assumes a single service per type would need to change. One part is *cinderclient()* in [nova/volume/cinder.py](https://raw.githubusercontent.com/openstack/nova/HEAD/nova/volume/cinder.py)

This (and the next method) have the advantage of not requiring cinder or neutron changes, as the provider portion of the UUID could be stripped before sending to the remote service.

### Other ID modifications
If we are considering changing the format, it might even be desirable to make other modifications for better maintainability and error checking (like prepending a type) or by switching to a more compact UUID representation using base64 or base92 (paying some penance for the increased identifier size introduced here). An ID containing these attributes might look like:

    volume;https://cinder.provider.com:2600/v2;BFoy536rS4e8h9e9FI9FWA==

## Separate provider string

Similar to prepending a provider string, we add an argument to all calls accepting a volume/instance/etc ID that specifies the endpoint. This would require more code modifications than Augmented UUIDs and changes to the REST APIs to support an extra argument, in addition to the changes that would be needed for Augmented UUIDs.

## Keep a lookup table

For each new UUID, maintain a lookup table in either nova or cinder-api. This would have the downside of
needing to somehow inform the installation as to where the ID came from, likely
through a separate call. It does have a lot of API compatibility, however, which is
an advantage.
