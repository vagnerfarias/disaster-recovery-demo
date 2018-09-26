#Disaster Recovery with RHV and Gluster

## Failover processes

Crash Primary site:
```
ansible-playbook failure/fail-prod.yml
```

Manually run a last data sync (in prod, a cron task would be run according to defined RPO)
```
python /usr/share/glusterfs/scripts/schedule_georep.py rhv-sd1 dr-gluster01.example.com rhv-sd1-dr
```

Execute the failover playbook
```
ansible-playbook playbooks/failover.yml --tags "fail_over"
```

## When Primary site is up again

In this demo environment, run the playbook to start services
```
ansible-playbook failure/start.yml
```

Ensure Gluster isn't georeplicating from primary to secondary and that RHV isn't writing to the Gluster storage domain
```
ansible-playbook playbooks/cleanup.yml --tags "clean_engine"
```

Ensure Secondary is georeplicating to Primary
```
ansible-playbook playbooks/reverse_georep.yml
```

## When Ready to Failback to Primary

Execute the failback playbook - requires shutting down VMs on secondary
```
ansible-playbook playbooks/failback.yml --tags "fail_back"
```


