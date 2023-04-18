### Error handling

When a playbook fails to execute on a host, you can use the --limit flag to execute only against hosts where the playbook failed.

You can also allow failure conditions to be defined using the failed_when keyword

There is also changed_when that allows an override of what ansible considers changed

The debug module allows to help troubleshoot plays
