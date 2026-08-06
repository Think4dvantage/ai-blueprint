# Prompt: Add a New SQLite Table

Use this prompt when you need to persist new structured data.

---

Add a new SQLite table for `{entity}` following the project conventions:

1. **Define the ORM model** in `src/[package]/database/models.py`:
   ```python
   class Entity(Base):
       __tablename__ = "entities"
       id = Column(String, primary_key=True, default=lambda: str(uuid.uuid4()))
       name = Column(String, nullable=False)
       created_at = Column(DateTime, default=datetime.utcnow)
   ```

2. **New table**: nothing further to do — `Base.metadata.create_all()` in `init_db()` creates it on next boot.
   **New column on an existing table**: add an idempotent guard in `_run_column_migrations()`
   in `src/[package]/database/db.py`:
   ```python
   cols = {row[1] for row in conn.execute(text("PRAGMA table_info(entities)")).fetchall()}
   if "new_col" not in cols:
       conn.execute(text("ALTER TABLE entities ADD COLUMN new_col TEXT"))
       conn.commit()
       logger.info("Migration: added entities.new_col column")
   ```
   No Alembic, no `.sql` files, no `_migrations` table — see `02-backend-conventions.md`.

3. **Add the Pydantic schemas** (Create / Update / Out) in `src/[package]/models/`.

4. **Add CRUD endpoints** in the appropriate router (or create a new one — see `add-api-router.md`).

5. **Update `.ai/context/architecture.md`** to document the new table or column in the "SQLite Tables" section.

6. **Sync**: Run `.ai/prompts/sync.md` to ensure human-readable docs are updated.
