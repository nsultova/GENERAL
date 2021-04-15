# Python packaging tutorial

A small tutorial based on my own journey with packaging gitcrawl. 

[Gitcrawl](https://pypi.org/project/gitcrawl/) is a tool which I wrote recently and spend some time on figuring out the right parameters. Thus I decided to write this small walk-through with explanations for my future me, my colleagues and anyone who might find it useful :)
If you just want to know how its done, just jump straight to Building the package

If you just want to know how its done, just jump straight to **Building the package**
 
 
### What does a package contain?

* a collection of modules
* the documentation
* any top-level scripts
* any additional data files required
* build and install instructions
 
Simplified output of `tree ../gitcrawl` pointing out the structure:
```
../gitcrawl
├── LICENSE
├── README.md
├
├── docs
│   │...<docs build with sphinx>
│
├── gitcrawl
│   ├── __init__.py
│   ├── core_functions.py
│   ├── docs
│   │   ├── ... < docs build with sphinx>
│   ├── ... <the actual code>
│   └── tests
│       └── <contains test code>
├
├── gitcrawl-env.yml
├── imgs
│   └── <images for the docs/README.md>
├── requirements.txt
├── setup.py
```
 
### The different components explained:
 
`LICENSE`: There are various and every project should contain one. [Here](https://choosealicense.com) you can get help to choose the right licence for your project.
This project here uses the [MIT License](https://snyk.io/learn/what-is-mit-license/)
 
`README.md`: Should contain a brief overview of the package and its usage. Format is Markdown.
 
 
`docs/`: The documentation. In this case, I decided to use [sphinx](https://www.sphinx-doc.org/en/master/). This is also the reason for the package containing two `docs/` directories. (Docs with sphinx will be covered in a future tutorial)
 
`gitcrawl/`: The main package – this is where the code goes.
 
`tests/` : Testcode to ensure the functionality of your code. See [pytest](https://docs.pytest.org/en/stable/unittest.html) for more information
 
`gitcrawl-env.yml` : Optional, relevant if you and your target audience use [conda](https://docs.conda.io/en/latest/)
 
`imgs/` : Optional, relevant if you want to include image files in your README.md or documentation
 
`requirements.txt` : Optional, relevant if you and your target audience use [pip](https://realpython.com/what-is-pip/)
 
 
`setup.py`: The build script for `setuptools` containing all neccesary informations for building and installing a package. See section **create setup.py** for more.
 
 
`MANIFEST.in`: Optional, description of what non-code files to include - yet to be included for this project, see [here](https://packaging.python.org/guides/using-manifest-in/)
 
 
## Building the package
 
### Creating setup.py
 
Make sure setuptools is installed in your working environment.
Then create a setup.py which should contain similar content. (adapted to your package and needs ofc)
 
```
import setuptools
 
with open("README.md", "r") as fh:
   long_description = fh.read()
 
setuptools.setup(
   name="gitcrawl",
   version="0.1.2",
   author="Neda Sultova",
   author_email="my.email@address.com",
   description="A small package for creating os-agnostic conda environment.yml files",
   long_description=long_description,
   long_description_content_type="text/markdown",
   url="https://github.com/nsultova/gitcrawl",
   packages=setuptools.find_packages(),
   package_data={"gitcrawl": ["python_module_index.pickle", "module-names.txt"]},
   install_requires=[
       "beautifulsoup4",
       "tqdm",
       "requests",
       "PyYAML",
       "requirements-parser",
       "pip-search",
       "findimports"
   ],
   entry_points={
       'console_scripts':[
           'gitcrawl=gitcrawl:main'
       ]
   },
   classifiers=[
       "Programming Language :: Python :: 3",
       "License :: OSI Approved :: MIT License",
       "Operating System :: OS Independent",
   ],
   python_requires='>=3.6',
)
```
setup() takes several arguments:
 
`name` is the distribution name of your package.  Be sure to update this with your username to avoid name-collisions.
 
`version` is the package version see [PEP 440](https://www.python.org/dev/peps/pep-0440) for more information.
 
`author` and `author_email `are used to identify the author of the package.
 
`description` is a short, one-sentence summary of the package.
 
`long_description` is a detailed description of the package. In this case, the long description is loaded from README.md.
 
`url` is the URL for the homepage of the project. For many projects, this will just be a link to GitHub, GitLab etc.
 
`packages` is a list of all Python import packages that should be included in the Distribution Package. Here find_packages() is usee to automatically discover all packages and subpackages.
 
`package_data`: Optional, in this case it contains two additional files that are required for the fuctioning of the package.
 
`install_requires ` is a setuptools setup.py keyword that should be used to specify what a project minimally needs to run correctly. When the project is installed by pip, this is the specification that is used to install its dependencies. Look [here](https://packaging.python.org/discussions/install-requires-vs-requirements/) for further information.
 
`entry_points`: Optional, allows the package to define a user-friendly name for installers of the package to execute
 
`classifiers` gives the index and pip some additional metadata about your package. See [here](https://pypi.org/classifiers/) for more information.
 
### Generating distribution archives
The next step is to generate distribution packages for the package for later upload.
 
Make sure you have  `setuptools`, `wheel` and `twine` installed, if not, execute:
`python3 -m pip install --user --upgrade setuptools wheel twine`
 
 
If you're building a newer version of your package, open `setup.py` and change version number accordingly
 
 
From the same directory where `setup.py` is located, execute:
`python3 setup.py sdist bdist_wheel`
 
This command  outputs a lot of text and generates following files in the (also newly created) `dist` directory:
```
dist/
   gitcrawl-0.1.0-py3-none-any.whl
   gitcrawl-0.1.0.tar.gz
```
 
Execute checks after build: 
`twine check dist/*`
 
 
The `tar.gz` file is a Source Archive whereas the `.whl` file is a Built Distribution. Newer pip versions preferentially install built distributions, but will fall back to source archives if needed.
 
### Uploading the distribution archives
 
Now the freshly baked package can be uploaded to the [Python Package Index](https://pypi.org)
 
First you can upload your package to [Test PyPI](https://test.pypi.org) and test if everything works. See [here](https://packaging.python.org/guides/using-testpypi/) for more information
 
Register an account on Test PyPI [here](https://test.pypi.org/account/register/) and for PyPI [here](https://pypi.org/) and follow the instructions.
 
The next steps are similar for Test PyPI and PyPi.
 
#### create PyPI API token
 
Go [here](https://test.pypi.org/manage/account/#api-tokens)  and create a new API token, don’t limit its scope to a particular project, since you are creating a new project.
 
Don’t close the page until you have copied and saved the token — you won’t see that token again.
 
### Upload distribution packages
Run twine to upload all of the archives under `dist/`:
 
`twine upload --repository GITCRAWL dist/*`
 
 
You will be prompted for a username and password. For the username, use `__token__`. For the password, use the token value, including the `pypi-` prefix.
 
After the command completes, you should see output similar to this:
 
```
Uploading distributions to https://upload.pypi.org/legacy
Enter your username: __token__
Enter your password: <tokenval comes here>
Uploading gitcrawl-0.1.0-py3-none-any.whl
100%|█████████████████████| 4.65k/4.65k [00:01<00:00, 2.88kB/s]
Uploading gitcrawl-0.1.2.tar.gz
100%|█████████████████████| 4.25k/4.25k [00:01<00:00, 3.05kB/s]
```
 
Once uploaded your package should be viewable on PyPi, like [here](https://pypi.org/project/gitcrawl/)
 
### Usage
You can use pip now to install your package and verify that it works. :)
 
### Notes
 
The final result. After executing everything, executing `tree gitcrawl`  shows the package structure
 
```
../gitcrawl
├── LICENSE
├── README.md
├── build
│   ├── bdist.macosx-10.9-x86_64
│   └── lib
│       └── gitcrawl
│           ├── __init__.py
│           ├── core_functions.py
│          ...
│           ├── module-names.txt
│           └── python_module_index.pickle
├── dist
│   ├── gitcrawl-0.1.0-py3-none-any.whl
│ ...
│   └── gitcrawl-0.1.2.tar.gz
├── docs
│   ├── ...
│   ...
├── gitcrawl
│   ├── __init__.py
│   ├── __pycache__
│   │   ...
│   ├── docs
│   │   ...
│   ├── gitcrawl.py
│   ├── ...
│   └── tests
│       └── ...
├── gitcrawl-env.yml
├── gitcrawl.egg-info
│   ├── ...
├── imgs
│   └── workflow.png
├── requirements.txt
├── setup.py
```
### Ressources:
Meanwhile the official docs have been updated for the better, so make sure to check them out too!

https://packaging.python.org/tutorials/packaging-projects/