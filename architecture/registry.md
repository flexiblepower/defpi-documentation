# Registry

The registry is the datastore of Docker images containing service implementations. Via labels set in the image, the implemented interfaces are described.

The registry can either be specified as a Docker Store organization, this requires the services to be available for this organization.

Private hosted Docker registries are also supported. In this case the services should share the same repository prefix. 