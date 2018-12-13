```
<%#
kind: PXELinux
name: Kickstart default PXELinux
oses:
- CentOS 4
- CentOS 5
- CentOS 6
- CentOS 7
- Fedora 16
- Fedora 17
- Fedora 18
- Fedora 19
- Fedora 20
- RedHat 4
- RedHat 5
- RedHat 6
- RedHat 7
%>
DEFAULT linux

LABEL linux
    KERNEL <%= @kernel %>
    <% if @host.operatingsystem.name == 'Fedora' and @host.operatingsystem.major.to_i > 16 -%>
    APPEND initrd=<%= @initrd %> ks=<%= foreman_url('provision')%> ks.device=bootif network ks.sendmac
    <% elsif @host.operatingsystem.name != 'Fedora' and @host.operatingsystem.major.to_i >= 7 -%>
    APPEND initrd=<%= @initrd %> ks=<%= foreman_url('provision')%> network ks.sendmac
    <% else -%>
    APPEND initrd=<%= @initrd %> ks=<%= foreman_url('provision')%> ksdevice=bootif network kssendmac
    <% end -%>
    IPAPPEND 2
```