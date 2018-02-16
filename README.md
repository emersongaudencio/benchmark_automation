# Benchmark Automation
Scripts to help automate the running of repeatable benchmarks. Currently only
sysbench is supported.

## Dependencies
* sysbench 1.0.12-1 or higher - [installtion
   instructions](https://github.com/akopytov/sysbench#installing-from-binary-packages).


## Usage
### Running sysbench MySQL benchmark
No direct input args, behavior is controlled by the following environment
variables:
* _EXP_NAME : Experiment name. Optional, defaults to 'sysbench'
* _TESTS : Quoted list of the tests to run, i.e. "oltp_read_only oltp_read_write"
* _THREADS : Quoted list of the # of threads to use for each run, i.e. "16 32"
* _TABLES : Number of tables to use for the tests
* _SIZE : Quoted list of the table sizes to use, in rows, i.e. "100000 1000000 10000000"

Any actual input argument will be passed as is to sysbench, so you can run it
as below:
```bash
_TESTS="oltp_read_only oltp_read_write" _THREADS="16 32" _TABLES=16 _SIZE="1000 10000" ./run_sbmysql.sh --rand-type=pareto --mysql-host=sbhost --mysql-db=sbtest --time=7200

_EXP_NAME=sample _TESTS=oltp_read_write _THREADS="1 2 4" _TABLES=64 _SIZE="10 100" ./run_sbmysql.sh --mysql-user=sysbench --mysql-password=sysbench --mysql_table_engine=innodb --rand-type=pareto --mysql-db=sbtest --time=60
```

### Generating graphs
Data from the benchmark run is stored in CSV format under directories named by
after the test names specified by `_TESTS` above.

Graphs can be generated by running the graph generation script ./sbmysql_csv_to_png.py

```bash
python ./sbmysql_csv_to_png.py plot_graph
Usage: sbmysql_csv_to_png.py plot_graph [OPTIONS] [CSV_FILES]...

Options:
  -o, --output TEXT       The directory where to output the graphs. Any
                          existing file would be overwritten
  -c, --plot-fields TEXT  The fields in the CSV file for which to plot the
                          graphs. Supported fields are: tps, qps, reads,
                          writes, response_time
  --help                  Show this message and exit.
```

An example invocation of the script is shown below
```bash
python ./sbmysql_csv_to_png.py plot_graph -o output/thr-16 -c tps -c qps -c response_time 4_4_82-sjc1.thr.16.sz.5000000.test.oltp_read_write.txt 4_4_112-sjc1.thr.16.sz.5000000.test.oltp_read_write.txt 4_4_115-sjc1.thr.16.sz.5000000.test.oltp_read_write.txt
```

The `_EXP_NAME` environment variable needs to be setup correctly to generate
mult-series graph. In the example above, `_EXP_NAME` was set to the hostname
when running the `run_sbmysql.sh` script.
