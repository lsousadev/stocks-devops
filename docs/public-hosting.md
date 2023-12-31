## Public Hosting

- create instance for hosting containers
    - t2.micro
    - elastic IP (for DNS)
    - security group:
        - inbound rules:
            - ssh   | 22/tcp    | <dev ip>  | ipv4/6
            - http  | 80/tcp    | 0.0.0.0/0 | ipv4/6
            - https | 443/tcp   | 0.0.0.0/0 | ipv4/6
            - ping  | ICMP      | 0.0.0.0/0 | ipv4/6
- install docker and docker-compose
- https://phoenixnap.com/kb/docker-nginx-reverse-proxy
- https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal