# Prometheus ideas

---

**Title:** Integrate Prometheus Metrics with Micrometer in `pom.xml`

**Description:** We need to enhance our application's observability by exposing metrics for Prometheus scraping. This task involves adding the necessary Prometheus and Micrometer adapter dependencies to our `pom.xml` file. The implementation should ensure that metrics are exposed on a management port (9090) separate from our main application port. This will allow us to monitor our application's health and performance without impacting its operation.

**Acceptance Criteria:**
1. Add the Prometheus metrics starter and Micrometer adapter dependencies to the `pom.xml`.
2. Configure the application to expose metrics on management port 9090.
3. Ensure that the `/actuator/prometheus` endpoint is accessible on port 9090, exposing the application's metrics in a format that Prometheus can scrape.
4. Metrics exposure should not impact the application's performance.
5. Documentation: Update the project's README or relevant documentation to include information on how to access the metrics endpoint and any necessary configuration changes.
6. Unit tests should be added or updated to cover the new functionality.
7. Integration tests should verify that the metrics endpoint is accessible and Prometheus can scrape data from it.

**Technical Notes:**
- The Maven dependencies to be added are:
  - `spring-boot-starter-actuator` for exposing actuator endpoints.
  - `micrometer-registry-prometheus` for Prometheus metrics support.
- Sample dependency snippet for `pom.xml`:
  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
  <dependency>
      <groupId>io.micrometer</groupId>
      <artifactId>micrometer-registry-prometheus</artifactId>
  </dependency>
  ```
- Consider security implications of exposing metrics and ensure only authorized personnel can access the metrics endpoint.

**Assignee:** [Developer's Name]

---

---

**Title:** Develop PromQL Queries for Monitoring HTTP Requests and JVM Threads

**Description:** In order to enhance our monitoring capabilities and gain deeper insights into our application's performance and health, we need to develop specific PromQL queries. These queries will target metrics exposed by our application through Prometheus, focusing on HTTP request duration and JVM thread metrics. The goal is to utilize the `http_server_requests_seconds_count` metric for monitoring the count of HTTP requests over time, and the `jvm_threads_live_threads` metric for tracking the number of live JVM threads. These queries will be used in our Prometheus adapter configuration to enable real-time monitoring and alerting based on these key performance indicators.

**Acceptance Criteria:**
1. Develop a PromQL query that calculates the total count of HTTP requests over a specified time window using the `http_server_requests_seconds_count` metric.
2. Develop a PromQL query to monitor the current number of live JVM threads using the `jvm_threads_live_threads` metric.
3. Ensure that both queries are optimized for performance and accuracy, considering the potential volume of data.
4. Test the queries within our Prometheus environment to confirm they return expected results without any errors.
5. Document the queries, including descriptions of what they measure and how they can be used within our monitoring and alerting framework.
6. Provide examples of how these queries can be integrated into dashboard visualizations or alerting rules.
7. Review the potential impact of these queries on Prometheus performance, ensuring they do not lead to excessive resource consumption.

**Technical Notes:**
- When developing the PromQL query for `http_server_requests_seconds_count`, consider filtering or aggregating by labels such as `method`, `status`, or `uri` if specific insights are required.
- For `jvm_threads_live_threads`, the query may be straightforward, but consider if aggregation over time or comparison against thresholds is necessary for alerting.
- Sample PromQL starting points:
  - For HTTP request count: `sum(rate(http_server_requests_seconds_count[5m])) by (method, status)`
  - For live JVM threads: `jvm_threads_live_threads`
- Review Prometheus documentation on PromQL for best practices on query optimization and efficiency.

**Assignee:** [Developer's Name]

---

---

**Title:** Integrate Custom PromQL Queries into Prometheus Adapter ConfigMap

**Description:** As part of our ongoing efforts to improve application monitoring and scalability, we need to integrate custom PromQL queries into our Prometheus adapter's configuration. This task involves updating the Prometheus adapter's ConfigMap with the PromQL queries developed for monitoring HTTP request counts and live JVM thread counts. The successful execution of this task will enable our Kubernetes cluster to utilize these metrics for horizontal pod autoscaling (HPA), as well as enhance our monitoring dashboards and alerting systems.

**Acceptance Criteria:**
1. Update the Prometheus adapter ConfigMap to include the new PromQL queries for `http_server_requests_seconds_count` and `jvm_threads_live_threads`.
2. Ensure that the queries are correctly formatted and placed within the `rules` section of the adapter's configuration.
3. Validate the ConfigMap changes by applying them in a controlled environment to avoid any disruption to current monitoring or scaling operations.
4. Confirm that the Prometheus adapter correctly interprets the queries and that the custom metrics are being reported as expected.
5. Demonstrate that these custom metrics can be queried through the Kubernetes API and are available for use in HPA or other monitoring tools.
6. Document the process of updating the ConfigMap and any relevant details about the queries within the team's knowledge base or relevant documentation.
7. Coordinate with the development and operations teams to ensure a smooth rollout of the changes to production environments, including any necessary monitoring to catch potential issues early.

**Technical Notes:**
- The ConfigMap update involves modifying the `prometheus-adapter` ConfigMap, typically found within the `custom-metrics` section. 
- Example snippet for the ConfigMap:
  ```yaml
  rules:
    - seriesQuery: 'http_server_requests_seconds_count{job="<job_name>"}'
      resources:
        template: <<.Resource>>
      name:
        matches: "^(.*)$"
        as: "http_requests_total"
      metricsQuery: 'sum(rate(<<.Series>>{<<.LabelMatchers>>}[2m])) by (<<.GroupBy>>)'

    - seriesQuery: 'jvm_threads_live_threads{job="<job_name>"}'
      resources:
        template: <<.Resource>>
      name:
        matches: "^(.*)$"
        as: "jvm_live_threads"
      metricsQuery: 'avg(<<.Series>>{<<.LabelMatchers>>}) by (<<.GroupBy>>)'
  ```
- Ensure to replace `<job_name>` with the appropriate value for your environment.
- Test thoroughly in a non-production environment before rolling out to avoid any potential impact on monitoring or autoscaling capabilities.

**Assignee:** [DevOps Team Member's Name]

---

---

**Title:** Conduct Stress and Performance Testing to Determine HPA Thresholds

**Description:** As we integrate custom metrics into our Prometheus adapter for autoscaling purposes, it is crucial to accurately determine the thresholds at which our application should scale horizontally. This task is assigned to the QA team to conduct comprehensive stress and performance testing on our application. The goal is to identify how the application performs under various loads, specifically focusing on the metrics `http_server_requests_seconds_count` and `jvm_threads_live_threads`. These insights will inform our HPA configuration, ensuring our application scales efficiently and maintains performance under varying loads.

**Acceptance Criteria:**
1. Design and execute stress tests that gradually increase the load on the application, monitoring `http_server_requests_seconds_count` and `jvm_threads_live_threads` metrics.
2. Document the application's performance at different load levels, identifying any bottlenecks or performance issues.
3. Determine the optimal thresholds for HPA based on the metrics observed during testing, ensuring the application can handle sudden increases in traffic without degradation of service.
4. Provide recommendations for HPA configuration settings, including minimum and maximum pod counts, and the specific metrics thresholds that should trigger scaling.
5. Create a detailed report of the testing process, results, and recommendations for the HPA configuration. This report should be accessible to both the DevOps and development teams for review and action.
6. Ensure that any critical issues identified during testing are highlighted and communicated to the development team for optimization before finalizing HPA thresholds.
7. Coordinate with the DevOps team to implement a pilot phase for the proposed HPA settings in a controlled environment, validating the effectiveness of the scaling thresholds under simulated traffic patterns.

**Technical Notes:**
- Utilize tools such as JMeter, Gatling, or Locust for stress testing, ensuring a variety of scenarios are covered, from typical usage patterns to peak load and beyond.
- Consider integrating Prometheus metrics directly into the performance testing suite to automate the collection of relevant data points.
- Collaboration with the development and DevOps teams is essential to understand any known limitations and to ensure the testing environment closely mirrors production.

**Assignee:** [QA Team Lead's Name]

---
