**Breadcrumb**

[Home](../home.md) > Pathway to Production > [Security Considerations](security.md)

# Security Considerations

A production deployment will need to harden both source code as well as the environment.

## HIPAA

A production CloudPRO deployment will contain patient data meaning that environments should align to HIPAA compliance. This includes several important pieces, including selection of only HIPAA eligble services. Design for a properly secured landing zone and environment controls must be enacted as well. Accelerators such as HLZA (https://aws.amazon.com/blogs/industries/introducing-landing-zone-accelerator-for-healthcare/) could be utilized to speed up environment deployment.

## Audit

Audit & Traceability is crucial for every system. Proper notification and alerting must be configured within a production environment.

## DR

A robust backup strategy must be implemented in the production system to help ensure resiliency. Evaluate composite SLA requirements and beset approach to meet these requirements.