# Contributing

Thank you for contributing to this Linux kernel source fork. Kernel changes require care because they can affect system stability, ABI behavior, and security.

## Before changing code

- Identify the relevant upstream subsystem and maintainer.
- Review `MAINTAINERS` and the subsystem documentation.
- Base the change on the intended upstream or fork baseline.
- Use a dedicated branch for each issue or feature.

## Patch expectations

- Keep commits small and logically complete.
- Explain why the change is needed, not only what changed.
- Do not mix unrelated cleanup with functional fixes.
- Preserve upstream formatting and naming conventions.
- Update documentation and tests with behavior changes.
- Avoid removing upstream files or attribution unless the change is deliberate and justified.

A kernel commit should normally include:

```text
subsystem: concise summary

Motivation and background.

Describe behavior before and after the change.

Include tests and hardware or VM information when relevant.

Signed-off-by: Your Name <email@example.com>
```

Use `scripts/checkpatch.pl` for patch style and follow `Documentation/process/submitting-patches.rst` for detailed conventions.

## Testing

Run focused checks for the changed subsystem. Examples:

```bash
make defconfig
make -j"$(nproc)"
scripts/checkpatch.pl --no-tree <patch>
```

Use `tools/testing/selftests` or subsystem-specific tests where available. Report the kernel configuration and test environment in the pull request.

## Submitting

Open a pull request with:

- the problem and expected behavior;
- the upstream baseline or kernel revision;
- the implementation summary;
- build, test, and hardware/VM results;
- known limitations or follow-up work; and
- whether the change is fork-specific or intended for upstream.

## Security

Do not open a public issue for an exploitable vulnerability. Follow upstream kernel vulnerability reporting procedures and coordinate privately until a fix is available.
