# AGENTS.md — Ansible Role Development

This file instructs AI coding agents on conventions, constraints, and
priorities when working in this Ansible role repository.

---

## Core Philosophy

- Prefer readability over cleverness.
- Roles solve **system logic** only — not business logic.
- Business logic belongs in playbooks that compose roles.
- A role should do one thing and do it well.
- `ansible-lint` must pass without errors or warnings.
- Only use your own file manipulation commands, not `sed` or `awk`.

---

## Development Order

When generating or modifying role content, work in this sequence:

1. `meta/main.yml` — understand or clarify the role's intent first
2. `defaults/main.yml` — define the public API (user-facing variables)
3. `vars/main.yml` — derive internal/computed variables
4. `tasks/`, `templates/`, `files/` — implement the actual behaviour
5. `molecule/` — verify the role's intent, not just its syntax

---

## meta/main.yml

- `galaxy_info.description` must clearly state **what the role does**,
  not how.
- List all supported distributions under `galaxy_info.platforms`.
  Only list distributions that are actually tested via Molecule.
- **Do not use `dependencies`**. Implicit role dependencies surprise users
  and complicate debugging. Document required roles in README instead.

---

## defaults/main.yml (User Variables)

- Every variable must be **prefixed with the role name**
  (e.g. role `storage` → variables named `storage_*`).
  This avoids variable bleeding across roles.
- Choose types deliberately: string, int, bool, list, or dict.
  Changing a variable's type later is a breaking change.
- Each variable must have a comment above it describing its effect.
- Keep defaults sensible and safe — a role should work out of the box
  without any customisation.

Example:
```yaml
# The filesystem type to use when formatting partitions.
storage_filesystem: ext4

# Whether to mount partitions automatically at boot.
storage_mount_on_boot: true
```

---

## vars/main.yml (Internal Variables)

- Used for **computed or distribution-specific** values the user
  should not need to override.
- Use lookup maps to resolve OS-family differences:
```yaml
_storage_packages:
  default:
    - util-linux
    - parted
  Suse:
    - util-linux
    - parted
    - gptfdisk

storage_packages: >-
  {{ _storage_packages[ansible_facts['os_family']]
     | default(_storage_packages['default']) }}```

- Apply the same pattern to service names, users, groups, config
  paths, and conditional logic.
- Avoid `ansible.builtin.set_fact` — resolve values here instead.
- Never use Jinja expressions inside `tasks/`; move them here.

---

## tasks/

### Naming
- Task names must express **intent**, not implementation.
  - ✅ `- name: Ensure storage packages are installed`
  - ❌ `- name: apt install util-linux parted`

### Formatting
- Write tasks **vertically** — one key per line.
  This makes multi-value conditionals and notify lists readable
  and makes it obvious they are AND-lists.
```yaml
# ✅ Readable
- name: Ensure configuration file is present
  ansible.builtin.template:
    src: storage.conf.j2
    dest: /etc/storage.conf
    owner: root
    group: root
    mode: "0644"
  when:
    - storage_configure
  notify:
    - Restart storage service

# ❌ Cramped
- name: Place config
  ansible.builtin.template:
    src: storage.conf.j2
    dest: /etc/storage.conf
  when: storage_configure
  notify: Restart storage service
```

### Module usage
- Always use **FQCNs** (`ansible.builtin.template`, not `template`).
- Prefer purpose-built modules over `shell` or `command`.
- `ansible.builtin.file` tasks must always set `owner`, `group`,
  and `mode`.
- Never install packages with `state: latest` unless the role's
  explicit purpose is upgrades.

### Validation
- Add `tasks/assert.yml` and validate every variable in
  `defaults/main.yml` using `ansible.builtin.assert`.
- Also define argument specs in `meta/argument_specs.yml`.
- Fail fast and early — bad input should be caught before any
  system changes are made.

### Error handling
- Use `block` / `rescue` for recoverable error handling.
- Never use `ignore_errors: true` or `failed_when: true` —
  find the root cause instead.
- Never use `=` where `:` is valid YAML syntax.

### Facts
- Only set `gather_facts: true` when the role actually uses facts.

---

## handlers/

- Do not use the `listen` directive. Reference handlers by their
  exact name only.
- Handler names must be title-case and descriptive:
  `Restart storage service`, not `restart`.

---

## templates/

- Every template must start with:
```
  {{ ansible_managed | comment }}```
  Use the correct comment syntax for the file type (e.g. `#` for
  shell/ini, `//` for nginx, `<!-- -->` for XML).
- Avoid Jinja logic in templates where possible — move logic to
  `vars/main.yml` and pass clean values in.
- Exceptions are simple loops over lists of items.

---

## files/

- Prefer templates over static files — even if the file has no
  variables today, it likely will.
- Only use `files/` for true binary or immutable assets.
- Remember: Ansible is not a package manager. Avoid bundling
  compiled binaries.

---

## Collections & Requirements

- Use FQCNs for all modules and plugins.
- List every required collection in `requirements.yml`.
- Pin collection versions where stability matters.

---

## Molecule

- `molecule/default/converge.yml` must exercise all meaningful
  variable combinations from `defaults/main.yml`.
- `molecule/default/verify.yml` must test the **role's intent**:
  - Is the service running and enabled?
  - Does the port respond?
  - Can the installed tool report its version?
  - Is the config file syntactically valid?
- Do not test implementation details (e.g. "does file X exist")
  without also testing observable behaviour.
- Every distribution listed in `meta/main.yml` must have a
  corresponding Molecule scenario or platform entry.

---

## What Agents Should NOT Do

- Do not add role dependencies in `meta/main.yml`.
- Do not use `set_fact` to derive values — use `vars/main.yml`.
- Do not inline Jinja in tasks — move it to `vars/main.yml`.
- Do not use `shell` or `command` when a module exists.
- Do not use `ignore_errors` or `failed_when: true`.
- Do not leave variables undocumented in `defaults/main.yml`.
- Do not skip the assert task when adding a new variable.
- Do not use short module names (without FQCN).
