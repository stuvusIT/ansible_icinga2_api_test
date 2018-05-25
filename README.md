# icinga2-api-test

This role installs a script which checks if a remote Icinga 2 API is avaiable.
This is accomplished by connecting with certificates and querying the status of the PostgreSQL connection.
If one of this steps fails (or the database connection isn't established), `msmtp` is used to notify administrators about it.
A systemd timer is used to call this script automatically.
Once the failed message is sent, a file is created to prevent more messages to be sent.
Once the API is available again, the file is removed again, and another message will be sent once the API fails again.

The API user only needs the `status/query` permission.

## Requirements

Ubuntu with a configured [msmstp](https://github.com/stuvusIT/msmtp) installation

## Role Variables

| Name                             | Default / Mandatory             | Description                                                                      |
|:---------------------------------|:-------------------------------:|:---------------------------------------------------------------------------------|
| `icinga2_api_test_calendar`      | `minutely`                      | `systemd.time(7)` specification for the test schedule                            |
| `icinga2_api_test_url`           | `127.0.0.1:5665`                | API URL to connect to. Remember that this must match the name in the certificate |
| `icinga2_api_test_cacert`        | `/var/lib/icinga2/certs/ca.crt` | Path to the CA certificate to validate the server with                           |
| `icinga2_api_test_pubkey`        | :heavy_check_mark:              | Path to the public key for the API client                                        |
| `icinga2_api_test_privkey`       | :heavy_check_mark:              | Path to the private key for the API client                                       |
| `icinga2_api_test_msmtp_account` | `default`                       | msmtp account to use (if multiple are configured)                                |
| `icinga2_api_test_msmtp_from`    | :heavy_check_mark:              | Sender address for mails                                                         |
| `icinga2_api_test_recipients`    | :heavy_check_mark:              | List of message recipients                                                       |
| `icinga2_api_test_failed_file`   | `/tmp/icinga.failed`            | Path to a file which prevents notifications if it exists                         |
| `icinga2_api_test_curl_flags`    |                                 | Additional flags to `curl` (you can disable certificate verification here)       |

## Example Playbook

```yml
- hosts: icinga-checkers
  roles:
    - role: icinga2-api-test
      icinga2_api_test_url: icinga-master.example.com:5665
      icinga2_api_test_pubkey: /etc/icinga-api.crt
      icinga2_api_test_privkey: /etc/icinga-api.key
      icinga2_api_test_msmtp_from: icinga@example.com
      icinga2_api_test_recipients:
        - alice@example.com
        - bob@example.com
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).

## Author Information

- [Janne Heß](https://github.com/dasJ)
