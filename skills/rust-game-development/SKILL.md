---
name: rust-game-development
description: Game development in Rust with Bevy ECS. Use when building games with entity-component-system architecture, managing game state, handling input, rendering 2D/3D scenes, or structuring game logic with systems and resources.
---

# Rust Game Development with Bevy

Based on Bevy documentation, examples, and the Bevy Cheat Book.

## When to Use This Skill

- Building games with the Bevy engine
- Understanding ECS (Entity-Component-System) architecture
- Managing game state and transitions
- Handling player input
- Spawning entities and configuring components
- Writing systems that query and transform game data

## Quick Start

```toml
[dependencies]
bevy = "0.18"
```

```rust
use bevy::prelude::*;

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .add_systems(Startup, setup)
        .add_systems(Update, (move_player, check_collisions))
        .run();
}

fn setup(mut commands: Commands) {
    commands.spawn(Camera2d::default());
    commands.spawn((
        Sprite { color: Color::srgb(0.3, 0.3, 0.9), ..default() },
        Transform::from_xyz(0.0, 0.0, 0.0),
        Player,
        Health(100),
    ));
}
```

## ECS Core Concepts

### Entities

Unique IDs that group components:

```rust
// Spawn an entity with components
let entity = commands.spawn((
    Transform::default(),
    Visibility::default(),
    Player,
    Name::new("Hero"),
)).id();

// Despawn
commands.entity(entity).despawn();
```

### Components

Data attached to entities:

```rust
#[derive(Component)]
struct Player;

#[derive(Component)]
struct Health(f32);

#[derive(Component)]
struct Velocity(Vec2);

#[derive(Component)]
struct Enemy {
    kind: EnemyKind,
    aggro_range: f32,
}
```

### Systems

Functions that process entities:

```rust
fn movement_system(
    time: Res<Time>,
    mut query: Query<(&Velocity, &mut Transform)>,
) {
    for (velocity, mut transform) in &mut query {
        transform.translation += velocity.0.extend(0.0) * time.delta_secs();
    }
}
```

### Resources

Global singleton data:

```rust
#[derive(Resource)]
struct Score(u32);

#[derive(Resource)]
struct GameSettings {
    difficulty: f32,
    sound_volume: f32,
}

fn update_score(mut score: ResMut<Score>) {
    score.0 += 10;
}
```

## Queries

### Basic Queries

```rust
// Read components
fn print_names(query: Query<&Name, With<Player>>) {
    for name in &query {
        println!("{}", name);
    }
}

// Mutable access
fn damage_enemies(mut query: Query<&mut Health, With<Enemy>>) {
    for mut health in &mut query {
        health.0 -= 10.0;
    }
}
```

### Query Filters

```rust
// With/Without
Query<&Transform, (With<Player>, Without<Dead>)>

// Changed/Added
Query<&Health, Changed<Health>>  // only entities whose Health changed this frame
Query<&Name, Added<Name>>       // only newly added

// Or
Query<&Transform, Or<(With<Player>, With<NPC>)>>
```

### Multiple Queries (Disjoint)

```rust
fn combat(
    players: Query<(&Transform, &Attack), With<Player>>,
    mut enemies: Query<(&Transform, &mut Health), With<Enemy>>,
) {
    for (player_tf, attack) in &players {
        for (enemy_tf, mut health) in &mut enemies {
            let distance = player_tf.translation.distance(enemy_tf.translation);
            if distance < attack.range {
                health.0 -= attack.damage;
            }
        }
    }
}
```

## Events

```rust
#[derive(Event)]
struct CollisionEvent {
    entity_a: Entity,
    entity_b: Entity,
}

fn detect_collisions(
    query: Query<(Entity, &Transform, &Collider)>,
    mut events: EventWriter<CollisionEvent>,
) {
    // ... detect and send events
    events.send(CollisionEvent { entity_a, entity_b });
}

fn handle_collisions(mut events: EventReader<CollisionEvent>) {
    for event in events.read() {
        println!("Collision: {:?} hit {:?}", event.entity_a, event.entity_b);
    }
}
```

## Input Handling

```rust
fn player_input(
    keyboard: Res<ButtonInput<KeyCode>>,
    mut query: Query<&mut Velocity, With<Player>>,
) {
    let mut direction = Vec2::ZERO;
    if keyboard.pressed(KeyCode::KeyW) { direction.y += 1.0; }
    if keyboard.pressed(KeyCode::KeyS) { direction.y -= 1.0; }
    if keyboard.pressed(KeyCode::KeyA) { direction.x -= 1.0; }
    if keyboard.pressed(KeyCode::KeyD) { direction.x += 1.0; }

    for mut velocity in &mut query {
        velocity.0 = direction.normalize_or_zero() * SPEED;
    }
}

fn mouse_click(buttons: Res<ButtonInput<MouseButton>>) {
    if buttons.just_pressed(MouseButton::Left) {
        // fire projectile
    }
}
```

## Game States

```rust
#[derive(States, Default, Debug, Clone, PartialEq, Eq, Hash)]
enum GameState {
    #[default]
    Menu,
    Playing,
    Paused,
    GameOver,
}

fn main() {
    App::new()
        .init_state::<GameState>()
        .add_systems(OnEnter(GameState::Playing), setup_game)
        .add_systems(OnExit(GameState::Playing), cleanup_game)
        .add_systems(Update, game_logic.run_if(in_state(GameState::Playing)))
        .run();
}

fn pause_game(
    keyboard: Res<ButtonInput<KeyCode>>,
    mut next_state: ResMut<NextState<GameState>>,
) {
    if keyboard.just_pressed(KeyCode::Escape) {
        next_state.set(GameState::Paused);
    }
}
```

## Plugins

Modular game organization:

```rust
pub struct CombatPlugin;

impl Plugin for CombatPlugin {
    fn build(&self, app: &mut App) {
        app.add_event::<DamageEvent>()
            .add_systems(Update, (apply_damage, check_death));
    }
}

// In main
App::new()
    .add_plugins((DefaultPlugins, CombatPlugin, UIPlugin))
    .run();
```

## Reference Map

- `references/ecs-patterns.md` — entity hierarchies, bundles, system ordering
- `references/rendering-assets.md` — sprites, meshes, textures, asset loading
- `references/game-patterns.md` — state machines, timers, spawning, physics

## Key References

- [Bevy Book](https://bevyengine.org/learn/book/introduction/)
- [Bevy Cheat Book](https://bevy-cheatbook.github.io/)
- [Bevy Examples](https://github.com/bevyengine/bevy/tree/main/examples)
