# Fail Faster: Adding Circuit Breakers to your APIs - Craig Freeman, Kenzan

Created: November 12, 2021 2:27 PM
Finished: No
Source: https://www.youtube.com/watch?v=_xufvNPLJ24&ab_channel=node.js
Tags: #nodejs

## Notes

- James Lewis and Martin Fowler
- [https://www.martinfowler.com/microservices](Fail%20Faster%20Adding%20Circuit%20Breakers%20to%20your%20APIs%20-%20ee53163ce1b8484d975c08f199188d6f.md)
- Design for Failure
    - Granceful Degradation - fallbacks minimize network traffic
    - Monitoring - streaming metrics, logs for early alerting
    - Resiliency - recover quickly from errors
- Circuit breakers
    - States
        - Closed - timeout or not available
        - Open - not available (breaker trips)
        - Half-open - wait threshold met (resource was tried again)
    - Failure Threshold - will break after some percentage of failing requests
    - Timeout - How long to circuit trips in a long time request
    - Wait threshold - How long a circuit stays broken