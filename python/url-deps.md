# Defining setup.py dependencies using a URL

Any branch on GitHub can be installed by pip by finding the URL to the zip export of that branch.

`https://github.com/<owner>/<repository>/archive/refs/heads/<branch>.zip`

The solution is to use packagename @ URL, like this:

```python
setup(
    name="photos-to-sqlite",
    ...
    install_requires=[
        "sqlite-utils>=2.7",
        "boto3>=1.12.41",
        "osxphotos>=0.28.13 ; sys_platform=='darwin'",
    ]
    extras_require={
        "test": ["pytest", "black", "hypothesis", "cogapp"],
        "docs": [
            "furo",
            "sphinx-autobuild",
            "codespell",
            "sphinx-copybutton",
            "beanbag-docutils @ https://github.com/simonw/beanbag-docutils/archive/refs/heads/bytes-in-url.zip",
        ]
)
```
