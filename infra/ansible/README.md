## Installing

- On OS X, run:
```
brew install ansible
brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb
```

`sshpass` is not available in Homebrew due to security considerations. We're using `sshpass` to login into the guest VM after logging in into the host VM, so it doesn't present a security risk in our case.

- On Ubuntu 12.04, run `sudo ./ansible_install.sh`

## Preparing 
To configure all prerequisites needed for running the tests, run this command:

```sh
ansible-playbook -K -i hosts prepare.yml
```

Note: you must specify sudo password for this command

## Running

Modify 'hosts' file to containing your hosts configuration. 
It is currently configured to run on localhost.

and then run:

```sh
ansible-playbook -i hosts playbooks/<some_playbook>.yml
```

## Additional arguments

Here are some arguments you can add to `ansible-playbook` invocations:

| Argument | Meaning |
| --- | --- |
| `--tags host` | Perform only host tests |
| `--tags guest` | Perform only guest tests |
| `--skip-tags clean` | When replaying, skip the clean actions so there is no need to re-build the packages |
| `--tags qemu` | Perform only QEMU execution tasks (without running all the lengthy setup and build tasks) |

## Customizing

It is possible to customize variables per host. For example, if you prefer to store the hda.img in /tmp and to use KVM hardware acceleration:

```
tester1.my-hosting.com hda_dir=/tmp qemu_machine=accel=kvm
```

## Useful variables
Here are some arguments you can add to `run_full_setup.yml` invocations (with '-e' or '--extra-vars')

Simulator role arguments:
| Variable | Meaning |
| --- | --- |
| `git_in_ansible`| Determines whether ansible playbook should pull simulator repo. Defaults to git_in_ansible=false |
| `workspace` | Directory where all related projects are located. Defaults to workspace={{ ansible_env.HOME }}|
| `dest` | Simulator output directory. Defaults to dest={{ workspace }}/simulator |
| `ref` | Used where git_in_ansible=true, determines which ref to pull from repo. Defaults to ref=refs/heads/master |
| `build_ssd_monitor` | Determines whether to build ssd_monitor. Defaults to build_ssd_monitor=false |
| `qemu_pid_file` | Location of pid_file. Defaults to "{{ ansible_env.HOME }}/simulator_qemu.pid" |
| `cleanup_only` | This argument is used internally. Determines if to skip test's preparations steps and to perform cleanup steps only. Defaults to cleanup_only=false |

Guest tester role arguments:
| Variable | Meaning |
| --- | --- |
| `short_mode` | Perform tests in short/long mode (default: true) |

Guest tester pre (preparation) role arguments:
| Variable | Meaning |
| --- | --- |
| `memory`| Parameter to qemu. Default: 2048 |
| `smp`| Parameter to qemu. Default: 4 |
| `hda_dir` | Where to store hda image if downloaded. This has to be a directory with ~ 9GB free space. Default: {{ ansible_env.HOME }} |
| `hda_cmpr` | compressed hda image filename. Default: {{hda_dir}}/hda_clean.qcow2.bz2 |
| `hda_img` | hda image filename. Default: {{hda_dir}}/hda_clean.qcow2 |
| `guest_ssh_port` | Port to guest machine, passed to qemu. Default: 2222 |
| `kernel_image` | filename of kernel image. Default: vmlinuz-3.8.0-29-generic |
| `initrd_image` | filename of initrd image. Default: initrd.img-3.8.0-29-generic |
| `kernel_cmdline` | kernel cmdline. Default: "BOOT_IMAGE=/boot/{{kernel_image}} root=UUID=063018ec-674c-4c3e-a976-ac4fa950864f ro" |
| `qemu_machine` | Default: accel=kvm |
| `test_instance_id` | For future use. This is intended to be passed from e.g. Jenkins to allow multiple tests to run simultaneously. Default: some_unique_id |

Host tester role arguments:
| Variable | Meaning |
| --- | --- |
| `short_mode` | Perform tests in short/long mode (default: true) |

Prepare role arguments:
| Variable | Meaning |
| --- | --- |
| `build_ssd_monitor` | Determines whether to build ssd_monitor. Defaults to build_ssd_monitor=false |

## TODO

- Add a public key to the guest VM image's `~esd/.ssh/authorized_keys` and get rid of `sshpass`.
