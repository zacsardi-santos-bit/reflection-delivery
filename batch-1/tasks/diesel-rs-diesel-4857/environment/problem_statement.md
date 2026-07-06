## Description

When defining a struct for database updates in Diesel, it's possible to embed another struct's fields using a special attribute. This lets you group related columns together logically. However, this currently only works when the embedded struct is always present — there is no support for making the embedded struct optional.

## Expected Behavior

Developers should be able to wrap an embedded struct in an optional type. The behavior should be:

- When the embedded value is present, all of its fields should be included in the database update, alongside the parent struct's own fields.
- When the embedded value is absent, those database columns should not be touched at all — their current values should remain unchanged.

## Current Behavior

Attempting to use an optional embedded struct in an update changeset either fails to compile or does not correctly handle the presence/absence of data. There is no mechanism for "optionally including a group of columns" in an update operation through embedding.

## Why This Matters

This limitation makes it difficult to express partial updates where some groups of columns may or may not need to change depending on context. Being able to make an embedded group of fields optional is a natural extension of the existing embedding feature and would make it significantly more flexible for real-world update scenarios.
