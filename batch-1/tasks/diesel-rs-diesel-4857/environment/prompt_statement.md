I'm using Diesel's derive functionality to define structs for database updates. I have a use case where I want to group several columns into an embedded sub-struct, and then make that embedded struct optional — so that when I provide it, all the embedded fields get updated, but when I leave it absent, those columns should be left completely unchanged in the database.

Right now this doesn't work. When I try to embed a struct wrapped in an optional type (marking the field with the embed attribute), the update either fails to compile or doesn't behave correctly. I'd expect that when the embedded value is present the columns are updated, and when it's absent they're simply skipped, leaving the existing database values intact.

Could you add support for optional embedded structs in Diesel's update changeset derive functionality?
