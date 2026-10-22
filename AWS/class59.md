# Class 59: Amazon Route 53
class Url: https://youtu.be/h87omH_kLko
## 1. What is Route 53?

- Amazon Route 53 is a highly available and scalable DNS web service provided by AWS.
- It converts domain names into IP addresses.
- It routes users to applications hosted on AWS or other platforms.
- It supports DNS health checks and traffic routing policies.

**Example:** `www.example.com` → `192.0.2.1`

## 2. What is DNS?

- DNS stands for **Domain Name System**.
- It maps domain names to IP addresses.
- It helps users access websites using domain names instead of IP addresses.

### DNS Resolution

The process of converting a domain name into an IP address.

**Example:** `www.google.com` → IP address of Google's server.

## 3. Hostname, Host ID, and Network ID

- **Hostname:** Name assigned to a computer or device on a network.
- **Host ID:** The portion of an IP address that identifies a host within a network.
- **Network ID:** The portion of an IP address that identifies the network.

## 4. Types of DNS Records in Route 53

### A Record
Maps a domain name to an IPv4 address.

Example: `example.com` → `192.0.2.1`

### AAAA Record
Maps a domain name to an IPv6 address.

### CNAME Record
Maps one domain name to another domain name.

Example: `www.example.com` → `example.com`

### Alias Record
Maps a domain name to supported AWS resources, such as:
- Application Load Balancer (ALB)
- Network Load Balancer (NLB)
- CloudFront distribution
- S3 website endpoint

**Note:** Alias records are specific to Route 53 and can be used at the root domain, unlike CNAME records.

## 5. Time to Live (TTL)

- TTL specifies how long a DNS response can be cached before it should be refreshed.
- TTL is measured in seconds.
- Higher TTL generally reduces DNS queries to Route 53.
- Lower TTL allows DNS changes to be reflected sooner after cached records expire.

**Example:** If TTL is 300 seconds, the DNS response can be cached for 5 minutes.

## 6. Latency

- Latency is the time taken for a request to travel to a server and receive a response.
- Lower latency generally means a faster response.

## 7. Route 53 Routing Policies

### 1. Simple Routing
Routes traffic to a single resource or a set of records without special routing rules.

### 2. Weighted Routing
Distributes traffic across multiple resources according to assigned weights.

**Example:**
- Server A: Weight 80
- Server B: Weight 20

Approximately 80% of traffic goes to Server A and 20% to Server B.

### 3. Latency-Based Routing
Routes users to the AWS Region that provides the lowest latency for them.

**Example:** Routes users to Mumbai or Singapore based on measured latency.

### 4. Failover Routing
Used to configure active-passive failover.

**Example:** If the primary server becomes unhealthy, traffic is routed to the secondary server.

### 5. Geolocation Routing
Routes traffic based on the geographic location of users.

**Example:** Users from India can be routed to an India-specific website.

### 6. Geoproximity Routing
Routes traffic based on the geographic location of users and resources. Traffic distribution can be adjusted using bias values.

### 7. Multivalue Answer Routing
Returns multiple healthy resource records in response to DNS queries.

### 8. IP-Based Routing
Routes traffic based on the IP address ranges of users.

## 8. Health Checks

- Route 53 health checks monitor the health of endpoints.
- They can check endpoints using HTTP, HTTPS, or TCP.
- Health checks can be used with routing policies such as failover.
- Route 53 can route traffic away from unhealthy endpoints when the routing configuration supports it.

## 9. Health Check Parameters

### Healthy Threshold
The number of consecutive successful health checks required to consider an endpoint healthy.

### Unhealthy Threshold
The number of consecutive failed health checks required to consider an endpoint unhealthy.

### Response Timeout
The maximum time Route 53 waits for a response to a health check before treating it as a failure.

### Health Check Interval
The frequency at which Route 53 performs health checks.

## 10. Example: Route 53 with ALB

1. User enters `www.example.com` in a browser.
2. DNS resolution looks up the domain name.
3. Route 53 returns the configured ALB destination through an Alias record.
4. The request reaches the ALB.
5. The ALB forwards traffic to healthy application targets.

## 11. Important Interview Questions

1. What is Amazon Route 53?
2. What is DNS resolution?
3. What is the difference between A and AAAA records?
4. What is the difference between CNAME and Alias records?
5. What is TTL in DNS?
6. What is the difference between weighted and latency-based routing?
7. What is the difference between geolocation and geoproximity routing?
8. How does Route 53 failover routing work?
9. What are Route 53 health checks?
10. How do you connect a domain name to an Application Load Balancer?

## Summary

- Route 53 is an AWS DNS service.
- DNS maps domain names to IP addresses or other DNS destinations.
- TTL controls DNS caching duration.
- Routing policies control how DNS responses direct users to resources.
- Health checks help identify unhealthy endpoints and support failover configurations.
