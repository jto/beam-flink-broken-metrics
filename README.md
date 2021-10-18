# Wordcount broken metrics demo

This project is demoing that Beam metrics are not reported when the job is executed with the Flink runner.

## How to run
1. Add the following lines to the configuration of your flink cluster:
    ```yaml
    metrics.reporter.prom.class: org.apache.flink.metrics.prometheus.PrometheusReporter
    metrics.reporter.prom.port: 9250-9260
    ```
1. Start the flink cluster
2. Go to [http://localhost:8081](http://localhost:8081) and make sure the cluster is running
3. Check the taskmanager log and look for the prometheus port (`Started PrometheusReporter HTTP server on port xxx`)
4. Execute the job by running:
    ```sh
      mvn package exec:java -Dexec.mainClass=org.apache.beam.examples.WordCount \
        -Dexec.args="--runner=FlinkRunner --flinkMaster=localhost:8081 --filesToStage=target/word-count-beam-bundled-0.1.jar  --output=/tmp/counts" \
        -Pflink-runner
    ```
5. Wait for the end of the execution.
6. Go to [http://localhost:prometheusPort](http://localhost:prometheusPort/). You will see all the metrics collected by the taskmanager
7. The Beam job should report metrics from a `Counter` called `emptyLines` and a `Distribution` called `lineLenDistro`.
8.  Those metrics are not reported by Flink