Make the module accept an entities map where each entity has its own mandatory tags + optional additional tags:
entities = {
"<friendly_name>" = { guid = "...", criticality = "...", tier = "...", user_journey = "...", user_flow = "...", additional_tags = {...} }
}
Then your module iterates over entities and applies tags per entity.
This solves:
different criticality/tier per entity
makes the plan output readable (you can see which entity is getting what)
supports future mandatory tags without breaking users (just extend the object)
