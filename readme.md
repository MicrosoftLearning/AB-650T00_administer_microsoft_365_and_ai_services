# AB-650: Administer Microsoft 365 and AI services

This repo contains the hands-on lab instructions and supporting files for course AB-650, Administer Microsoft 365 and AI services. The seven labs cover tenant operations, collaboration, identity, security, information protection, Microsoft 365 Copilot and Cowork, and agent governance.

| Learning path | Labs |
| --- | --- |
| 1: Configure and manage Microsoft 365 tenants and workloads | Lab 1: Operate the Microsoft 365 tenant<br>Lab 2: Enable collaboration and govern its content |
| 2: Govern and secure Microsoft 365 tenants and workloads | Lab 3: Administer identities and delegated access<br>Lab 4: Control sign-in and protect communications<br>Lab 5: Protect and govern information |
| 3: Manage and secure Microsoft 365 AI services | Lab 6: Roll out and administer Copilot and Cowork<br>Lab 7: Govern agents and operate AI services |

The labs are published at [microsoftlearning.github.io/AB-650T00_administer_microsoft_365_and_ai_services](https://microsoftlearning.github.io/AB-650T00_administer_microsoft_365_and_ai_services/). Start with [Get started](Instructions/Labs/00-getting-started.md).

| Folder | Contents |
| --- | --- |
| `Instructions/Labs` | Lab guides by learning path, plus sample case pages in `LP2/assets` |
| `Allfiles` | Files learners open or upload during the labs. In the hosted lab environment, this folder is the read-only **AllFiles (F:)** drive. |
| `_data`, `_layouts`, `assets/course` | GitHub Pages site navigation, layout, and styles |
| `tools` | `check_labs.py`, the content checks that run on every pull request |

## Changing a lab

After you edit a lab guide, run `python tools/check_labs.py --write` (requires Python and PyYAML). It updates the site navigation in `_data/course.yml` and checks front matter, exercise timings, links, `F:\` paths, and Allfiles usage. The same checks run in GitHub Actions on every pull request.

## Information for MCTs

**Are you an MCT?** - Have a look at our [GitHub User Guide for MCTs](https://microsoftlearning.github.io/MCT-User-Guide/)

Any MCT (Microsoft Certified Trainer) can submit a pull request to the content in this GitHub repo. Microsoft and the course author will then triage and include content changes as needed. You can submit bugs, changes, improvements, and ideas.
