# ECS Patterns

## Entity Hierarchies (Parent-Child)

```rust
fn spawn_character(mut commands: Commands) {
    commands.spawn((Transform::default(), Player, Name::new("Hero")))
        .with_children(|parent| {
            parent.spawn((Transform::from_xyz(0.0, 1.0, 0.0), Weapon));
            parent.spawn((Transform::from_xyz(0.0, 0.5, 0.0), HealthBar));
        });
}

// Query children
fn update_weapons(
    parents: Query<&Children, With<Player>>,
    mut weapons: Query<&mut Transform, With<Weapon>>,
) {
    for children in &parents {
        for &child in children.iter() {
            if let Ok(mut tf) = weapons.get_mut(child) {
                // update weapon position
            }
        }
    }
}
```

## System Ordering

```rust
App::new()
    .add_systems(Update, (
        input_system,
        movement_system.after(input_system),
        collision_system.after(movement_system),
        render_system.after(collision_system),
    ))
    // Or use system sets
    .configure_sets(Update, (
        GameSet::Input,
        GameSet::Movement.after(GameSet::Input),
        GameSet::Collision.after(GameSet::Movement),
    ))
    .add_systems(Update, move_player.in_set(GameSet::Movement))
```

## Commands (Deferred Mutations)

Commands are queued and applied at sync points:

```rust
fn spawn_enemies(mut commands: Commands, time: Res<Time>) {
    // Spawn
    commands.spawn((Enemy, Health(50.0), Transform::default()));

    // Modify existing entity
    commands.entity(entity).insert(Poisoned { duration: 5.0 });
    commands.entity(entity).remove::<Shield>();

    // Despawn with children
    commands.entity(entity).despawn_recursive();
}
```

## Marker Components

Zero-sized types for filtering:

```rust
#[derive(Component)]
struct Player;

#[derive(Component)]
struct Dead;

#[derive(Component)]
struct Invulnerable;

// Use in queries
fn damage(query: Query<&mut Health, (With<Enemy>, Without<Invulnerable>)>) { ... }
```

## Component Bundles

Group related components:

```rust
#[derive(Bundle)]
struct CharacterBundle {
    sprite: Sprite,
    transform: Transform,
    health: Health,
    velocity: Velocity,
}

// Spawn with bundle
commands.spawn(CharacterBundle {
    sprite: Sprite { color: Color::WHITE, ..default() },
    transform: Transform::from_xyz(100.0, 0.0, 0.0),
    health: Health(100.0),
    velocity: Velocity(Vec2::ZERO),
});
```

## Change Detection

```rust
fn on_health_change(query: Query<(Entity, &Health), Changed<Health>>) {
    for (entity, health) in &query {
        if health.0 <= 0.0 {
            // entity died
        }
    }
}

fn on_spawn(query: Query<Entity, Added<Enemy>>) {
    for entity in &query {
        println!("New enemy spawned: {entity:?}");
    }
}
```

## Run Conditions

```rust
fn main() {
    App::new()
        .add_systems(Update, (
            game_logic.run_if(in_state(GameState::Playing)),
            ai_system.run_if(not(in_state(GameState::Paused))),
            debug_overlay.run_if(resource_exists::<DebugMode>),
            spawn_wave.run_if(on_timer(Duration::from_secs(5))),
        ));
}
```

## Local Resources (System State)

Per-system persistent state:

```rust
fn count_frames(mut counter: Local<u32>) {
    *counter += 1;
    if *counter % 60 == 0 {
        println!("Second elapsed");
    }
}
```

## Observer Pattern (Bevy 0.14+)

React to component lifecycle events:

```rust
app.observe(on_enemy_added);

fn on_enemy_added(trigger: Trigger<OnAdd, Enemy>, query: Query<&Transform>) {
    let entity = trigger.entity();
    if let Ok(tf) = query.get(entity) {
        println!("Enemy spawned at {:?}", tf.translation);
    }
}
```
