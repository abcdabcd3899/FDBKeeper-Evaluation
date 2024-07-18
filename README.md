# Evaluation Scripts

Some scripts for setup benchmark enviroment for fdbkeeper.

## Usage

### Setup Local Depends

```
pipenv install
```

### Setup Hosts

1. Prepare at least 1 host for client, at least 1 host for foundationdb or zookeeper service.
2. Server requirements (See also [setup-ecnu-cloud playbook](./playbook/setup-ecnu-cloud.yml))
   1. Ubuntu 22.04 is recommended
   2. Mount ext4 fs on /var/lib/keeper-bench with options: `defaults,noatime,discard`
   3. Setup prometheus node exporter with textfile collector for `/var/lib/node_exporter`
   3. Setup prometheus pushgateway for `keeper-bench`.
3. Create ansible inventory file: `hosts.yml`. Put hosts in `clients` and `servers` groups. Here is a simple example:

   ``` yaml
   all:
   children:
      clients:
         hosts:
         c1:
            ansible_host: 1.2.3.4
         c2:
      servers:
         hosts:
         s1:
            fdb_coordinator: true
            fdb_procs: [transaction, storage]
         s2:
   vars:
      keeper_bench_path: /local/path/to/keeper-bench
      fdb_init_command: configure new triple ssd
   ```

### Setup Prometheus and Grafana

We use prometheus and grafana to collecting metrics.
You can use docker compose to deploy a simple prometheus:

```
# Create file sd configs if you setup hosts without `setup-ecnu-cloud` playbook.
touch docker/prom_file_sd.yml

# Up docker compose services
docker compose up -d

# Grafana is available on 0.0.0.0:9033
```

Open [127.0.0.1:9033](http://127.0.0.1:9033) and setup data source and dashboard manually.
Here is a [dashboad example](./dashboard-example.json)

### Deploy Benchmark Clients and Services

See [Makefile](./Makefile).
