---
layout: post
title:  "MySQL Connect Error"
date:   2019-04-30 08:00
---

# MySQL error

## Error: ER_NOT_SUPPORTED_AUTH_MODE: Client does not support authentication protocol requested by server; consider upgrading MySQL client

* �߻�
    * node.js �� ���� server side application �� connect �ÿ� �߻�
* ����
    * ���� ��
* �ذ�
    * mysql �� node.js version �� �� �¾Ƽ� ���� ����� ����ϴ� ������ ����
    * `mysql
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'RootPassword';
    `

    * �� sql�� �̿��� �ذ�

    * ���� ����Ʈ - https://stackoverflow.com/questions/50093144/mysql-8-0-client-does-not-support-authentication-protocol-requested-by-server