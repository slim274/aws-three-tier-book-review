# Three-Tier AWS Troubleshooting Case Notes

These cases were documented in the **Week 6 Assignment 4** capstone. Commands below are short verification patterns, not a runnable deployment recipe.

## Traffic path

`Internet → Public ALB → Web EC2/Nginx → Internal ALB → App EC2/Node.js → RDS MySQL`

## Issue 1: Nginx rejects configuration

- **Observed:** `unknown directive` on `sudo nginx -t`.
- **Reported cause:** non-breaking spaces from copied configuration text.
- **Action:** display invisible characters, rewrite the affected line/file, then validate.

```bash
sudo cat -A /etc/nginx/sites-available/default
sudo nginx -t
sudo systemctl reload nginx
```

## Issue 2: npm dependency installation stalled

- **Observed:** installation hanging at the `sharp` dependency stage.
- **Action taken:** cleared previous dependency state, repeated installation and checked Node version compatibility.
- Do not delete production lockfiles reflexively; the lab report recorded their removal as a troubleshooting step, not as a recommended CI practice.

## Issue 3: backend cannot connect to RDS

- **Reported cause:** misconfigured network security group.
- **Action:** permit DB port **3306** from the application security group only; verify DB endpoint used by the application. No credentials are included in this repository.

## Issue 4: ALB health checks fail

- **Reported cause:** wrong target port or health-check path.
- **Action:** confirm the Node app is running, check listening port (the report cites **3001**) and adjust target group configuration.

```bash
sudo ss -tulnp
curl -I http://localhost:3001
sudo nginx -t
```

## Integrity of claims

The submitted report mentions read-replica evidence headings but does not give enough independent, unambiguous proof for this repo to claim a deployed read replica. It documents Multi-AZ RDS and working three-tier routing.
