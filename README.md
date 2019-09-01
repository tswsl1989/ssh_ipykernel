# SSH Kernel - an ipykernel over ssh

A remote jupyterkernel via ssh

* Free software: MIT license

The ideas are heavily based on [remote_ikernel](https://bitbucket.org/tdaff/remote_ikernel), however `ssh_ipykernel`adds some important features

* `jupyter_client`s `write_connection_file` is used on the remote server to get free ports
* Local ports (obtained by jupyter also via `write_connection_file`) will be ssh forwarded to the remote ports
* The ssh connection and the tunnel command will be retried in case of network or similar errors

## Usage

* Usage of ssh kernel

  ```bash
  $ python -m ssh_ipykernel -h
  usage: __main__.py [--help] [--timeout TIMEOUT] [--env [ENV [ENV ...]]] [-s]
                    --file FILE --host HOST --python PYTHON

  optional arguments:
    --help, -h            show this help message and exit
    --timeout TIMEOUT, -t TIMEOUT
                          timeout for remote commands
    --env [ENV [ENV ...]], -e [ENV [ENV ...]]
                          environment variables for the remote kernel in the
                          form: VAR1=value1 VAR2=value2
    -s                    sudo required to start kernel on the remote machine

  required arguments:
    --file FILE, -f FILE  jupyter kernel connection file
    --host HOST, -H HOST  remote host
    --python PYTHON, -p PYTHON
                          remote python_path
  ```

* Creation of kernel specification

  ```python
  import ssh_ipykernel.manage
  ssh_ipykernel.manage.add_kernel(
      host="btest",
      display_name="SSH btest:demo(abc)",
      local_python_path="/opt/miniconda/envs/test36/bin/python",
      remote_python_path="/opt/anaconda/envs/python36",
      sudo=False,
      env="VAR1=demo VAR2=abc",
      timeout=10
  )
  ```

* Check of kernel specification

  ```bash
  $ jupyter-kernelspec list
  Available kernels:
    ssh__ssh_btest_demo_abc_         /Users/bernhard/Library/Jupyter/kernels/ssh__ssh_btest_demo_abc_

  $ cat /Users/bernhard/Library/Jupyter/kernels/ssh__ssh_btest_demo_abc_/kernel.json
  {
    "argv": [
      "/opt/miniconda/envs/test36/bin/python",
      "-m",
      "ssh_ipykernel",
      "--host",
      "btest",
      "--python",
      "/opt/anaconda/envs/python36",
      "--timeout",
      "10",
      "--env",
      "VAR1=demo VAR2=abc",
      "-f",
      "{connection_file}"
    ],
    "display_name": "SSH btest:demo(abc)",
    "language": "python"
  }
  ```

## Credits

The ideas are heavily based on

* [remote_ikernel](https://bitbucket.org/tdaff/remote_ikernel)
