= pg_tools

== Purpose

A gem that facilitates working with Postgresql schemas for multi-tenant applications. It provides rake tasks and hooks on create for your User model.
The advantage of using this gem over others is that it keeps your private schema migrations separate from the public ones (in db/migrate/private_schemas) and also keeps different schema.rb files in
db/schema.rb (public) and db/private/schema.rb (private) respectively.

== Inspiration & Motivation

http://aac2009.confreaks.com/06-feb-2009-14-30-writing-multi-tenant-applications-in-rails-guy-naor.html
http://blog.jerodsanto.net/2011/07/building-multi-tenant-rails-apps-with-postgresql-schemas/

== Details

This gem assumes you maintain your private schema migrations in db/migrate/private_migrations and your public ones in db/migrate.
It will dump schema.rb files inside 

== Usage

Add to your Gemfile:
  gem 'pg_tools', :git => 

Create a directory called "private_schemas" inside your db/migrate. This will hold your private migrations. Move any private migrations there.
Create a directory called "private" inside db/. This will hold the private schema.rb.

Your public (default search path) migrations will continue to live in db/migrate. Your public schema.rb will continue to live at its usual place in db/.

In your User model:
  acts_as_tenant_aware

This will provide the following functionality:
  - when a new user is created in the system, it will create the Postgresql schema and load the private db structure from db/private/schema.rb

== Rake Tasks

  tenants:db:migrate [VERSION=XY]
    Migrates each user's tables within their own private Postgresql schema
    Dumps db/private/schema.rb (Note: it assumes all private schemas are structurally the same, so it only dumps this once)
    Updates schema_migrations in the private schema, as well as in the default schema. (the latter is important, otherwise tools like RSpec will complain that there are unrun migrations)
   
  tenants:db:migrate_tenant USER_ID=XY
    Migrates the tenant specified by the given USER_ID passed
  
  tenants:schema:dump search_path=xy
    Dumps db/private/schema.rb for given search_path.  (Note: it assumes all private schemas are structurally the same, so this can always be overridden by other private schemas)

  tenants:schema:load search_path=xy [RAILS_ENV=env]
    Load the private db/private/schema.rb structure in the specified search_path, for the given Rails environment.

= TODOs:
  rake task to create db/private & db/migrate/private_schemas directories
  rails generate private migration
  Extend to allow different db structures for different users/private schemas 

