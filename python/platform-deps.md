# Use setup.py to install platform-specific dependencies

```python
setup(
    name="photos-to-sqlite",
    ...
    install_requires=[
        "sqlite-utils>=2.7",
        "boto3>=1.12.41",
        "osxphotos>=0.28.13 ; sys_platform=='darwin'",
    ]
)
```
So `; sys_platform=='darwin'` in the install requires line.

More details: https://www.python.org/dev/peps/pep-0508/#environment-markers
and https://hynek.me/articles/conditional-python-dependencies/
