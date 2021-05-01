Setting up Resilience4j dependency for OrderService application

Setting up the dependency

  <dependency>
            <groupId>io.github.resilience4j</groupId>
            <artifactId>resilience4j-spring-boot2</artifactId>
            <version>1.3.0</version>
        </dependency>

Configure the resilience4j configuration in application.yaml file

resilience4j:
  circuitbreaker:
    instances:
      inventoryservice:
        register-health-indicator: true
        ring-buffer-size-in-closed-state: 5
        ring-buffer-size-in-half-open-state: 3
        wait-duration-in-open-state: 30s
        failure-rate-threshold: 50
        record-exceptions:
          - java.io.IOException
          - java.util.concurrent.TimeoutException
          - org.springframework.web.client.ResourceAccessException
          - java.lang.IllegalStateException


Enable the actuator endpoints for circuit breaker

management:
  endpoint:
    health:
      show-details: always
  health:
    circuitbreakers:
      enabled: true
  endpoints:
    web:
      exposure:
        include: "*"

Add the CircuitBreaker annotation in the saveOrder method in OrderServiceImpl class

    @Override
    @CircuitBreaker(name = "inventoryservice")
    public Order saveOrder(Order order) {
            //discoveryClient();
            loadBalancedUsingRestTemplate();
            //inventoryFeignClient.updateQty();
            return this.orderDAO.save(order);
    }
