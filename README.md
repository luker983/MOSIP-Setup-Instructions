# MOSIP 1.1.2 CloudLab Sandbox Installation Instructions

Most of these instructions are taken from [MOSIP Sandbox Installer](https://docs.mosip.io/platform/build-and-deploy/sandbox-installer)

1. Use [MOSIP profile](https://raw.githubusercontent.com/fretbuzz/MOSIP-Setup-Instructions/main/cloudlab_specific/MOSIP%20Profile%20genlib%20Script.txt) to instantiate experiment on CloudLab. Make sure you have a temporary ssh key pair associated with your CloudLab profile because you will need to upload the private key later on.

2. Enable SELinux permissive mode on the console machine and reboot. [Instructions here](https://phoenixnap.com/kb/enable-selinux-centos)

3. Upload [installation script](/cloudlab_specific/MOSIP%20Installation%20Script.txt) to the console machine.

4. Follow instructions at top of script and run. It will probably throw a few errors, don’t worry about it yet.

5. In `/home/mosipuser/mosip-infra/deployment/sandbox-v2/`, run `./preinstall.sh` as `mosipuser`

6. Replace `hosts.ini` with [this one](/cloudlab_specific/hosts.ini)

7. Make sure `group_vars/all.yml` has `sandbox_domain_name` set to the hostname of your console machine.

8. In `group_vars/k8s.yml`, set `network_interface=eth1`

9. In `roles/packages/helm-cli/tasks/main.yml`, replace `https://kubernetes-charts.storage.googleapis.com` with `https://charts.helm.sh/stable` ([Source](https://stackoverflow.com/a/65404574/15117449))

10. In `roles/k8scluseter/kubernetes/node/meta/main.yml` *and* `roles/k8scluster/kubernets/master/meta/main.yml`, replace package names with `packagename-1.19.0`. For instance, `kubeadm` becomes `kubeadm-1.19.0`. 

11. As `mosipuser`, run `an site.yml` in `/home/mosipuser/mosip-infra/deployment/sandbox-v2`. 

**Note:** May need to run `source ~/.bashrc` to get the alias `an`. 

At this point MOSIP will start installing and take a very long time. Some installation issues can be resolved by simply re-running `an site.yml`.

## Troubleshooting

If you get the following error:
```
TASK [packages/crypto : Install python3 cryptography]
fatal: [console]: FAILED! => {"changed": false, "cmd": ["/bin/pip3", "install", "cryptography"], "msg": "stdout: Collecting cryptography\n  Downloading https://files.pythonhosted.org/packages/9b/77/461087a514d2e8ece1c975d8216bc03f7048e6090c5166bc34115afdaa53/cryptography-3.4.7.tar.gz (546kB)\n    Complete output from command python setup.py egg_info:\n    \n            =============================DEBUG ASSISTANCE==========================\n            If you are seeing an error here please try the following to\n            successfully install cryptography:\n    \n            Upgrade to the latest pip and try again. This will fix errors for most\n            users. See: https://pip.pypa.io/en/stable/installing/#upgrading-pip\n            =============================DEBUG ASSISTANCE==========================\n    \n    Traceback (most recent call last):\n      File \"<string>\", line 1, in <module>\n      File \"/tmp/pip-build-ifd1g9v2/cryptography/setup.py\", line 14, in <module>\n        from setuptools_rust import RustExtension\n    ModuleNotFoundError: No module named 'setuptools_rust'\n    \n    ----------------------------------------\n\n:stderr: WARNING: Running pip install with root privileges is generally not a good idea. Try `pip3 install --user` instead.\nCommand \"python setup.py egg_info\" failed with error code 1 in /tmp/pip-build-ifd1g9v2/cryptography/\n"}
```
Run:
* `source /home/mosipuser/.venv-py3/bin/activate`
* `python -m pip install setuptools_rust`
* `python -m pip install --upgrade pip`
* `deactivate` 

You may need to install more packages in this virtual environment, but the errors should tell you what is needed.
