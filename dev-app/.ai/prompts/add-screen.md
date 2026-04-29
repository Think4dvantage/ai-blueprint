# Prompt: Add a New Screen to an Existing Feature

Use this when adding a screen to a feature that already has its Cubit, repository, and routes set up.

---

## Checklist

- [ ] Create the screen file in `features/[feature]/presentation/screens/[screen_name]_screen.dart`
- [ ] Screen is a `StatelessWidget` (or `StatefulWidget` only if local ephemeral state is unavoidable)
- [ ] Use existing feature Cubit via `context.read<FeatureCubit>()` — do not create a new Cubit unless the screen has genuinely independent state
- [ ] `BlocBuilder` for reactive UI, `BlocListener` for side effects
- [ ] All colors and text styles come from `Theme.of(context)` — no hardcoded values
- [ ] Navigation via `context.go()` / `context.push()` — no `Navigator.push`

## Routing

- [ ] Add the new route to the feature's router file
- [ ] Route path defined as a constant

## Widget Test

- [ ] Widget test covering the main states the screen can display (loading, content, error)

## Documentation

- [ ] Add the screen to `context/architecture.md` screen map table
