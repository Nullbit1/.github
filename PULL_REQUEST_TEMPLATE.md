# Summary

Explain what this PR does in one or two sentences.

Fixes #<issue-number> (if applicable).

## Type of change

Put an `x` in the boxes that apply:

- [ ] Bug fix
- [ ] New feature
- [ ] Refactoring / cleanup
- [ ] Docs update
- [ ] Other (describe below)

## Details

Add any important technical details, design decisions or trade-offs here.
If the change is security-sensitive or touches low-level parts (hypervisor, kernel, memory parsing, etc.), call that out clearly.

## Testing

Describe how you tested this change:

- commands you ran;
- platforms / OS versions;
- any special setups (VM, nested virtualization, etc.).

Example:

```bash
cargo test --all --locked
./scripts/run-smoke-tests.sh
```

## Checklist

- [ ] I have read the CONTRIBUTING guide.
- [ ] I have run the relevant linters/formatters for this project.
- [ ] I have added or updated tests where it makes sense.
- [ ] I have updated the docs / README if needed.
- [ ] I did not include secrets, private data or unrelated changes in this PR.
