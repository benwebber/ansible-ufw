# ansible-ufw

**ansible-ufw** provides a high-level, service-based interface for configuring the [Uncomplicated Firewall (UFW)](https://wiki.ubuntu.com/UncomplicatedFirewall).

## Requirements

**ansible-ufw** requires:

* Ansible 1.9+
* Debian, Ubuntu, or other distribution providing the UFW package

## Installation

Install from [Ansible Galaxy](https://galaxy.ansible.com/detail#/role/4939).

```
ansible-galaxy install benwebber.ufw
```

## Usage

**ansible-ufw** provides atomic service firewall definitions that allow you to build a host's firewall iteratively.

For example, to allow Redis connections on a private interface:

```yaml
- roles:
  # Install UFW and configure the base policy. Include `benwebber.ufw` with no parameters.
  - benwebber.ufw
  # Open Redis (6379/tcp) on eth1.
  - role: benwebber.ufw
    service: redis
    interfaces:
      - eth1
```

When you apply the roles above, you'll see the following output:

```
TASK: [benwebber.ufw | install ufw] *****************************************************
ok: [redis1.example.org]

TASK: [benwebber.ufw | configure ufw logging] *******************************************
ok: [redis1.example.org]

TASK: [benwebber.ufw | set base ufw policy] *********************************************
ok: [redis1.example.org]

TASK: [benwebber.ufw | open 22/tcp ssh] *************************************************
ok: [redis1.example.org] => (item=eth0)

TASK: [benwebber.ufw | open 6379/tcp redis] *********************************************
ok: [redis1.example.org] => (item=eth1)
```

See [`tasks/services/`](tasks/services/) for included services. Pull requests gladly accepted.

### Assumptions

**ansible-ufw** makes the following assumptions:

1. Hosts deny all incoming traffic by default.
2. The default public interface is `eth0`.
3. SSH should be available on the default public interface.

All assumptions can be overridden by local variables.

### Services

### Examples

Install UFW and configure the base policy.

```yaml
- roles:
    - benwebber.ufw
```

Allow SSH traffic on the default interface (`eth0`). This task is part of the base policy.

```yaml
- roles:
  - benwebber.ufw
  - role: benwebber.ufw
    service: ssh
```

Allow HTTP traffic on multiple interfaces.

```yaml
- roles:
  - benwebber.ufw
  - role: benwebber.ufw
    service: http
    interfaces:
      - eth0
      - eth1
```

Deny DNS traffic on the default interface (`eth0`).

```yaml
- roles:
  - benwebber.ufw
  - role: benwebber.ufw
    service: domain
    delete: true
```

Create a custom rule using the special `local` service.

```yaml
- roles:
  - benwebber.ufw
  - role: benwebber.ufw
    service: local
    port: 24816
    proto: udp
    interfaces:
      - eth1
```

## Role variables

The following variable defaults are defined in `defaults/main.yml`. The default values are shown in brackets.

* `ufw_default_direction`

    default direction to secure (`incoming`)

* `ufw_default_policy`

    default traffic policy (`deny`)

* `ufw_default_state`

    default firewall state (`enabled`)

* `ufw_interfaces`

    list of default interfaces to control (`['eth0']`)

* `ufw_logging`

    whether firewall logging is enabled, and at what level (`off`)

* `ufw_open_ssh`

    whether to open SSH on the default interface by default (`true`)

* `ufw_service_default_direction`

    default direction to secure for services (`in`)

* `ufw_service_default_rule`

    default rule to apply to services (`allow`)

See the [ufw module](https://docs.ansible.com/ansible/ufw_module.html) documentation for more details.

## Contributing

Please feel free to submit pull requests for new services or general improvements.

## License

MIT
