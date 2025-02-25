# AWS Route 53

The Route 53 integration allows you to monitor [Amazon Route 53](https://aws.amazon.com/route53/)—a DNS web service.

Use the Route 53 integration to collect and parse logs related to DNS activity. Then visualize that data in Kibana, create alerts to notify you if something goes wrong, and reference logs when troubleshooting an issue.

For example, you could use the data from this integration to spot unusual activity in your network traffic routing. You could also use the data to troubleshoot the underlying issue by looking at additional context in the logs, such as the forwarding rules and DNS endpoints for relevant custom names.

## Data streams

The Route 53 integration collects one type of data: logs.

**Logs** help you keep a record of events happening in Amazon Route 53.
This integration collects Public Hosted Zone logs and Resolver logs. Logs collected by the Route 53 integration include the names being queried, the highest registered domain, the DNS response code, the edge location that served the request, and more. See more details in the [Logs reference](#logs-reference).

## Requirements

You need Elasticsearch for storing and searching your data and Kibana for visualizing and managing it.
You can use our hosted Elasticsearch Service on Elastic Cloud, which is recommended, or self-manage the Elastic Stack on your own hardware.

Before using any AWS integration you will need:

* **AWS Credentials** to connect with your AWS account.
* **AWS Permissions** to make sure the user you're using to connect has permission to share the relevant data.

For more details about these requirements, see the **AWS** integration documentation.

## Setup

Use this integration if you only need to collect data from the Route 53 service.

If you want to collect data from two or more AWS services, consider using the **AWS** integration.
When you configure the AWS integration, you can collect data from as many AWS services as you'd like.

For step-by-step instructions on how to set up an integration, see the
{{ url "getting-started-observability" "Getting started" }} guide.

## Logs reference

### Public Hosted Zone logs

The `route53_public_logs` dataset collects information about public DNS queries that Route 53 receives.

Query logs contain only the queries that DNS resolvers forward to Route 53. If a DNS resolver has already cached the response to a query (such as the IP address for a load balancer for example.com), the resolver will continue to return the cached response without forwarding the query to Route 53 until the TTL for the corresponding record expires.

Depending on how many DNS queries are submitted for a domain name (example.com) or subdomain name (www.example.com), which resolvers your users are using, and the TTL for the record, query logs might contain information about only one query out of every several thousand queries that are submitted to DNS resolvers.

See the [Route 53 Documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/query-logs.html) for more information.

{{event "route53_public_logs"}}

{{fields "route53_public_logs"}}

### Resolver logs

The `route53_resolver_logs` dataset collects all DNS queries & responses for:
* Queries that originate in Amazon Virtual Private Cloud VPCs that you specify, as well as the responses to those DNS queries.
* Queries from on-premises resources that use an inbound Resolver endpoint.
* Queries that use an outbound Resolver endpoint for recursive DNS resolution.
* Queries that use Route 53 Resolver DNS Firewall rules to block, allow, or monitor domain lists.

As is standard for DNS resolvers, resolvers cache DNS queries for a length of time determined by the time-to-live (TTL) for the resolver. The Route 53 Resolver caches queries that originate in your VPCs, and responds from the cache whenever possible to speed up responses. Resolver query logging logs only unique queries, not queries that Resolver is able to respond to from the cache.

For example, suppose that an EC2 instance in one of the VPCs that a query logging configuration is logging queries for, submits a request for accounting.example.com. Resolver caches the response to that query, and logs the query. If the same instance’s elastic network interface makes a query for accounting.example.com within the TTL of the Resolver’s cache, Resolver responds to the query from the cache. The second query is not logged.

See the [Route 53 Documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-query-logs.html) for more information.

{{event "route53_resolver_logs"}}

{{fields "route53_resolver_logs"}}