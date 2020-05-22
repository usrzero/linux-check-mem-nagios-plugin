check_mem
=========

Nagios plugin that measures Linux memory use as reported by the '/proc/meminfo' output using ssh access.

A [PNP4Nagios](https://github.com/lingej/pnp4nagios) graph template is include to combine the figures on a single chart.

![Sample Chart](check_mem_example.png)

This is plugin is based on one [published on the Nagios Exchange](http://exchange.nagios.org/directory/Plugins/System-Metrics/Memory/check_mem-2Esh/details) by Lukasz Gogolin <lukasz.gogolin@gmail.com>
This repo is a copy of version 1.1 released 2012-07-22 and modified by @whereisaaron.
This version 1.5 has contributions by @usrzero.

The changes brings plugin-less support (check via ssh).

Sample install steps below are for CentOS 6.5 and assume you are using nagio with check_nrpe plugin, nrpe, and pnp4nagios.

1) Install and configure nagios

2) Install the files for this plugin and chart
```
cp check_mem.php /usr/share/nagios/html/pnp4nagios/templates/
cp check_mem /usr/lib64/nagios/plugins/
/sbin/restorecon -v /usr/lib64/nagios/plugins/check_mem
```

3) Now add the command to nagios configuration /etc/nagios/objects/commands.cfg
```
define command {
        command_name    check_mem_by_ssh;
        command_line    /usr/lib64/nagios/plugins/check_mem_by_ssh -H $HOSTADDRESS$ -p $ARG1$ -l $ARG2$ -w $ARG3 -c $ARG4$;
}
```

4) Now add the service check to nagios server configuration /etc/nagios/servers/target_server.cfg
```
define service{
        use                             generic-service;
        host_name                       target_server;
        service_description             memory;
        check_command                   check_mem_by_ssh!22!nagios!75!90;
        check_interval                  4;
        retry_interval                  1;
}

```

5) Reload nagios 
```  
service nagios reload
```

6) Test manually
```
/usr/lib64/nagios/plugins/check_mem_by_ssh -H 0.0.0.0 -p 22 -l nagios -w 75 -c 90;
```


The pnp4nagios template name assumes you have 
[pnp4nagios configured with `CUSTOM_TEMPLATE = 1`](http://docs.pnp4nagios.org/pnp-0.6/tpl_custom?s[]=custom),
if not you may need to change this filename so that it matches. If you find that you get multiple 
charts instead of one, the template name is probably not matching.



