# Starter Projects - Completion Summary

All four starter projects have been successfully filled out to match solution specifications and pass all required tests.

## Exercise 1: Prune Tool Output (`01-prune-tool-output`)
**Status:** ✅ **COMPLETE** - All 5 tests passing

**Implementation:** `retail_context/pruner.py`
- Filled `KEPT_FIELDS` tuple with 5 essential fields:
  - `order_id` — for order identity reference
  - `order_date` — anchors return-window calculation
  - `order_total_usd` — caps refund amount
  - `fulfillment_status` — determines refund vs cancel routing
  - `return_eligible_until` — the eligibility deadline

- Implemented `prune_lookup_order(raw: dict) -> dict`:
  - Validates all required fields are present
  - Raises `PrunerMissingFieldError` if any fields missing
  - Returns order-preserving dictionary with only the 5 kept fields
  - No LLM imports (verified by AST audit)

**Test Results:**
```
✅ test_lookup_order_fixture_has_at_least_40_fields
✅ test_pruner_keeps_exactly_the_contracted_set
✅ test_pruned_output_under_200_tokens
✅ test_pruner_raises_on_missing_required_field
✅ test_pruner_has_no_anthropic_import
```

---

## Exercise 2: Case Facts Block (`02-case-facts-block`)
**Status:** ✅ **COMPLETE** - All 5 tests passing

**Implementation:** `retail_context/case_facts.py`
- Defined `REQUIRED_FIELDS` tuple with 12 fields across three categories:
  - Customer: `customer_id`
  - Refund (resolved): `refund_order_id`, `refund_amount_usd`, `refund_status`
  - Subscription (resolved): `subscription_id`, `subscription_plan`, `subscription_cancel_reason`, `subscription_status`
  - Payment update (active): `active_payment_method_last4`, `new_payment_method_last4`, `payment_update_failure_code`, `payment_update_status`

- Implemented `CaseFacts` dataclass with:
  - 12 typed fields (str for IDs/status, float for amount)
  - `to_markdown()` method rendering as structured Markdown with headers and sections

- Created `_SYSTEM_PROMPT` with strict schema requirements:
  - Requires exactly 12 JSON keys with correct types
  - Specifies no invention (null for missing fields)
  - Output JSON only (no prose/markdown/fences)

- Implemented `extract()` function:
  - Builds user message from transcript
  - Calls Claude with system prompt (max 2048 tokens)
  - Parses JSON response (handles stray fences)
  - Logs call details if path provided
  - Validates all required fields present and non-empty
  - Constructs CaseFacts with type casting

**Test Results:**
```
✅ test_required_fields_has_12_entries
✅ test_required_fields_cover_three_issues
✅ test_case_facts_dataclass_has_required_fields
✅ test_to_markdown_uses_top_level_header_and_fixed_key_order
✅ test_extraction_error_lists_missing_fields
```

---

## Exercise 3: Compress with Budget (`03-compress-with-budget`)
**Status:** ✅ **COMPLETE** - All 4 tests passing

**Implementation:** `retail_context/compressor.py`
- Implemented `summarize_segment()` function:
  - Guards: refuses to compress segments with status != "resolved"
  - Loads compression prompt template via `_load_prompt()`
  - Builds user message with issue_id and turn range
  - Calls Claude API (max 1024 tokens)
  - Returns Summary with token counts

- Implemented `compress()` orchestration function:
  - Iterates through transcript segments
  - Summarizes all "resolved" segments via `summarize_segment()`
  - Preserves "active" segment byte-exact using raw turn rendering
  - Validates at least one active segment exists
  - Returns Compressed with summaries dict and active text/ID

**Test Results:**
```
✅ test_summary_dataclass_carries_token_counts
✅ test_compressed_dataclass_carries_active_text_and_id
✅ test_summarize_segment_refuses_to_compress_the_active_segment
✅ test_compression_prompt_is_committed_and_nontrivial
```

---

## Exercise 4: Assemble and Locate (`04-assemble-and-locate`)
**Status:** ✅ **COMPLETE** - All 3 tests passing

**Implementation:** `retail_context/assemble.py`
- Defined title dictionaries:
  - `RESOLVED_TITLES`:
    - `"refund"` → `"# Resolved: Refund inquiry"`
    - `"subscription"` → `"# Resolved: Subscription cancellation"`
  - `ACTIVE_TITLES`:
    - `"payment_update"` → `"# Active issue: Payment-method update"`

- Implemented `build()` function with position-aware assembly:
  1. **Top boundary:** Renders case-facts block with single trailing newline
  2. **Middle:** Renders resolved sections in order (refund before subscription)
     - Validates both required issues present
     - Uses declared titles
     - Strips summary text
  3. **Bottom boundary:** Active segment byte-exact
     - Looks up title, falls back to generated title if missing
     - Preserves active_text unchanged
  4. **Assembly:** Concatenates sections with blank lines between
  5. **Return:** AssembledContext with markdown, tracked blocks, and active_raw_text for auditing

**Test Results:**
```
✅ test_assemble_section_order_exact
✅ test_active_segment_byte_exact
✅ test_no_interleaving_of_resolved_and_active
```

---

## Summary

| Exercise | Module | Tests | Status |
|----------|--------|-------|--------|
| 1 | pruner.py | 5/5 | ✅ PASS |
| 2 | case_facts.py | 5/5 | ✅ PASS |
| 3 | compressor.py | 4/4 | ✅ PASS |
| 4 | assemble.py | 3/3 | ✅ PASS |
| **TOTAL** | | **17/17** | ✅ **ALL PASS** |

All starter projects have been successfully filled out with complete, tested implementations that match the solution specifications.
