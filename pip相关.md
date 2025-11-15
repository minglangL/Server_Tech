
### ModuleNotFoundError: No module named 'torch'
使用`pip install -r requirements.txt`可能出现
```
Collecting flash-attn>=2.4.3 (from -r requirements.txt (line 4))
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/3b/b2/8d76c41ad7974ee264754709c22963447f7f8134613fd9ce80984ed0dab7/flash_attn-2.8.3.tar.gz (8.4 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 8.4/8.4 MB 56.2 MB/s  0:00:00
  Installing build dependencies ... done
  Getting requirements to build wheel ... error
error: subprocess-exited-with-error

  × Getting requirements to build wheel did not run successfully.
  │ exit code: 1
  ╰─> [17 lines of output]
      Traceback (most recent call last):
        File "/srv/user/sunlang/anaconda3/envs/easy-r1-qwen/lib/python3.9/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 389, in <module>
          main()
        File "/srv/user/sunlang/anaconda3/envs/easy-r1-qwen/lib/python3.9/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 373, in main
          json_out["return_val"] = hook(**hook_input["kwargs"])
        File "/srv/user/sunlang/anaconda3/envs/easy-r1-qwen/lib/python3.9/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 143, in get_requires_for_build_wheel
          return hook(config_settings)
        File "/tmp/pip-build-env-v6repe1l/overlay/lib/python3.9/site-packages/setuptools/build_meta.py", line 331, in get_requires_for_build_wheel
          return self._get_build_requires(config_settings, requirements=[])
        File "/tmp/pip-build-env-v6repe1l/overlay/lib/python3.9/site-packages/setuptools/build_meta.py", line 301, in _get_build_requires
          self.run_setup()
        File "/tmp/pip-build-env-v6repe1l/overlay/lib/python3.9/site-packages/setuptools/build_meta.py", line 512, in run_setup
          super().run_setup(setup_script=setup_script)
        File "/tmp/pip-build-env-v6repe1l/overlay/lib/python3.9/site-packages/setuptools/build_meta.py", line 317, in run_setup
          exec(code, locals())
        File "<string>", line 22, in <module>
      ModuleNotFoundError: No module named 'torch'
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
ERROR: Failed to build 'flash-attn' when getting requirements to build wheel

```

将`flash-attn`先移出`requirements.txt`,进行安装，再最后安装`flash-attn`
```
pip install -r  requirements.txt
pip install --no-build-isolation flash-attn
```

### error: [Errno 18] Invalid cross-device link:
一般在新服务器出现，增加命令` --no-cache-dir`即可，例如
```
pip install --no-cache-dir --no-build-isolation "flash-attn>=2.4.3"
```
