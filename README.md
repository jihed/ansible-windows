# ansible-windows

## Windows Remote Management should be already set on windows server 2012R (-- don't ask me more about windows -- )

"<powershell>\n",
"$admin = [adsi]('WinNT://./administrator, user')\n",
"$admin.PSBase.Invoke('SetPassword', '{{ windows_password | default(generated_windows_password) }}')\n",
"$scriptPath=((New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1'))\n",
"Invoke-Command -ScriptBlock ([scriptblock]::Create($scriptPath)) -ArgumentList '-skipNetworkProfileCheck'\n",
"</powershell>"

## Make sure to have all the needed packages on your host (here, is linux.)

    python-devel
    krb5-devel
    krb5-libs
    krb5-workstation
    python-pip
    gcc

## Configure Control Host for Kerberos Authentication
### configure the krb for ansible.

pip install pywinrm[kerberos]

cat << EOF > /etc/krb5.conf.d/ansible.conf

[realms]

 AD1.${GUID_CAP}.EXAMPLE.OPENTLC.COM = {

 kdc = ad1.${GUID}.example.opentlc.com
 }

[domain_realm]
 .ad1.${GUID}.example.opentlc.com = AD1.${GUID_CAP}.EXAMPLE.OPENTLC.COM
EOF


[root@bastion ~]# kinit administrator@AD1.${GUID_CAP}.EXAMPLE.OPENTLC.COM
Password for administrator@AD1.${GUID_CAP}.EXAMPLE.OPENTLC.COM:

[root@bastion ~]# klist
Ticket cache: KEYRING:persistent:0:krb_ccache_8oqoPvA
Default principal: Administrator@AD1.${GUID_CAP}.EXAMPLE.OPENTLC.COM

Valid starting       Expires              Service principal
10/03/2017 19:11:39  10/04/2017 05:11:39  krbtgt/AD1.${GUID_CAP}.EXAMPLE.OPENTLC.COM@ad1.${GUID}.EXAMPLE.OPENTLC.COM
	renew until 10/04/2017 19:11:37
