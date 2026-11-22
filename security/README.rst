Linux kernel security subsystem
===============================

This directory contains kernel security infrastructure and optional security
modules. It is not a standalone security product: the protections available at
runtime depend on the kernel configuration, boot parameters, userspace policy,
firmware, and platform. Enabling a feature does not by itself establish a
complete security boundary.

For user-facing documentation, start with ``Documentation/security/`` and the
specific subsystem documentation linked below. This file is a source-tree map
for developers.

Architecture and responsibilities
----------------------------------

The security subsystem combines several layers:

* **Core hooks and LSM framework** (``security.c``, ``lsm_init.c``,
  ``lsm_syscalls.c``): dispatch security decisions and initialize enabled
  Linux Security Modules (LSMs).
* **Capabilities and common checks** (``commoncap.c``): capability-based
  checks shared by the kernel and LSMs.
* **Major and stacked LSMs**: SELinux, AppArmor, Smack, TOMOYO, and other
  modules provide different policy models. The configured ``CONFIG_LSM``
  order and boot-time ``lsm=`` parameter determine which enabled modules are
  initialized; availability and stacking constraints vary by module.
* **Integrity and key management** (``integrity/``, ``keys/``): integrity
  measurement/appraisal/enforcement, trusted keys, and keyrings.
* **Other protections**: examples include lockdown, Landlock, Yama, LoadPin,
  SafeSetID, IPE, BPF LSM, and device-cgroup access control.

The top-level ``Kconfig`` and ``Makefile`` define configuration and build
wiring. A directory can be present in the source while its code is disabled
in a particular kernel configuration.

Subsystem map
-------------

=====================  =====================================================
Path                   Focus
=====================  =====================================================
``apparmor/``          Path-based mandatory access control
``bpf/``               BPF-based LSM integration
``integrity/``         Integrity framework, including IMA and EVM
``keys/``              Kernel keyrings and key management
``landlock/``          Unprivileged application sandboxing
``loadpin/``           Restricting kernel file loading to a trusted source
``lockdown/``          Restricting interfaces that can modify or expose the
                       running kernel
``selinux/``           Label-based mandatory access control
``smack/``             Simplified mandatory access control
``tomoyo/``            Domain-based mandatory access control
``yama/``              Additional discretionary access-control protections
``safesetid/``         Restrictions on selected UID/GID transitions
``ipe/``               Integrity Policy Enforcement
=====================  =====================================================

See each module's ``Kconfig``, ``Documentation/security/``, and
``MAINTAINERS`` entries for configuration, behavior, test ownership, and
maintainer information. The table is a guide, not a complete list of security
mechanisms or a recommendation to enable every module.

Configuration and boot behavior
--------------------------------

1. Configure ``CONFIG_SECURITY`` and only the LSMs and supporting features
   required for the target system.
2. Review ``CONFIG_LSM`` in the resulting ``.config``. LSM ordering can affect
   initialization and policy behavior; do not copy a list without checking
   module dependencies and the target's boot configuration.
3. Check the running system rather than inferring runtime policy from source
   configuration alone. Boot parameters, distribution policy, initramfs,
   userspace policy loading, and firmware state can change the effective
   protections.
4. Test policy changes on a disposable VM or dedicated test machine before
   deploying them. A deny-by-default policy or lockdown setting can prevent
   normal boot or administration.

Subsystem-specific behavior is authoritative in its own documentation. Do not
weaken an existing check or change an LSM hook's semantics to make a local
test pass without understanding the security boundary and obtaining review
from the relevant maintainers.

Development and validation
--------------------------

Security changes need tests that exercise both allowed and denied behavior,
including relevant privilege, namespace, configuration, and error paths.
Prefer tests close to the code being changed:

* LSM userspace API selftests:

  .. code-block:: sh

     make -C tools/testing/selftests TARGETS=lsm run_tests

* Other subsystem selftests live under ``tools/testing/selftests`` (for
  example, Landlock and the individual LSM directories). Check their local
  ``README`` and ``Makefile`` for prerequisites and supported test options.
* KUnit tests are configuration-dependent. For common capability tests,
  review ``CONFIG_SECURITY_COMMONCAP_KUNIT_TEST`` in ``security/Kconfig`` and
  the KUnit documentation in ``Documentation/dev-tools/kunit/``.
* Build the changed configuration and architecture; where practical, test
  with and without the relevant feature enabled. A successful build alone
  does not validate security behavior.

For code review, inspect the hook call path, credential and namespace context,
locking and lifetime rules, audit behavior, and interactions with stacked
modules. Keep fixes narrowly scoped, add regression coverage, and run
``scripts/checkpatch.pl`` on patches. Follow
``Documentation/process/submitting-patches.rst`` and the applicable
``MAINTAINERS`` entry when submitting upstream.

Security issue handling
-----------------------

Do not disclose details of an unpatched vulnerability in a public issue or
pull request. Follow the Linux kernel's current private vulnerability
reporting process for upstream code. For a fork-only change, contact the
repository owner privately and coordinate with upstream maintainers when the
issue also affects upstream. Avoid claiming a security guarantee based solely
on a test, configuration option, or code review.

Further reading
---------------

* ``Documentation/security/``
* ``Documentation/admin-guide/LSM/``
* ``Documentation/dev-tools/kunit/``
* ``Documentation/process/submitting-patches.rst``
* ``MAINTAINERS``
