---
ID: 387
post_title: 'RHCSA &#8211; Rsyslogd and Journald'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-rsyslogd-and-journald
published: true
post_date: 2015-05-10 00:00:00
---
In RHEL7 there are 3 main systems for capturing logs:

<ul>
	<li><strong>rsyslogd</strong> - Running processes/services usually send log messages to rsyslogd, which in turn usually writes them into log files in /var/log/... </li>
	<li><strong>journald</strong> - This system is relatively new and came as part of systemd. It logs all information that it receives from systemd. For example if systemd fails to startup a process, then this failure will get logged via journald. It actually logs all the same messages that rsyslogd logs.</li>

	<li>Write directly to a file. For example the apache software writes directly to log files, although it can be configured to pass log messages to rsyslogd instead. It's best to use rsyslog where possible for better consistency.</li>

</ul>