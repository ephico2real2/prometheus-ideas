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

