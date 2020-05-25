# Leka License Guide

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

## About

At Leka, we believe in open source. That's what most (if not all) of our work is licensed under Apache 2.0.

This guide details how to correctly license your work for the Leka project.

More detailed information can be found on the Apache Software Foundation website.

> [ASF - FREQUENT QUESTIONS ABOUT APACHE LICENSING](https://www.apache.org/foundation/license-faq.html)

## Setup a new project

When starting a new project, add a `LICENSE` file and a `NOTICE` file at the root of the project.

The `LICENSE` can be found here:

- [Apache License Version 2.0](https://www.apache.org/licenses/LICENSE-2.0.txt)

The `NOTICE` file for Leka must look like this:

```
Leka Project - [PRODUCT_NAME]
Copyright 2020 APF France handicap

This product includes software developed at
Leka @ APF France handicap (http://leka.io/
& https://www.apf-francehandicap.org/).

==========================================================================
Third party libraries used by the [PRODUCT_NAME] project:
==========================================================================
```

## Apply License to software

### Internal & Employee Contributions

It is valuable to tag each of your source code files in case they become detached from the `LICENSE` file. To apply the Apache License to your source code files, you must attach the following boilerplate notice as a comment at the top of the files.

```
Leka Project - [PRODUCT_NAME]
Copyright 2020 APF France handicap
SPDX-License-Identifier: Apache-2.0
```

### External Contributions

#### Sign the CLA

Before contributing, you must sign the CLA put in place. It is available here:

> [Leka @ APF France handicap Contribution License Agreement]() (*not available yet*)

#### Contribute

It is valuable to tag each of your source code files in case they become detached from the `LICENSE` file. To apply the Apache License to your source code files, you must attach the following boilerplate notice as a comment at the top of the files.

```
Licensed to APF France handicap (APF) under one
or more contributor license agreements. See the NOTICE file
distributed with this work for additional information
regarding copyright ownership. APF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied. See the License for the
specific language governing permissions and limitations
under the License.
```

Don't forget to also edit the `NOTICE` file with your contribution.
