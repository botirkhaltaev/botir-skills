# Game Patterns

## Timers

```rust
#[derive(Component)]
struct SpawnTimer(Timer);

fn spawn_enemies(
    time: Res<Time>,
    mut query: Query<&mut SpawnTimer>,
    mut commands: Commands,
) {
    for mut timer in &mut query {
        timer.0.tick(time.delta());
        if timer.0.just_finished() {
            commands.spawn((Enemy, Health(30.0), Transform::default()));
        }
    }
}

// Create timer
commands.spawn(SpawnTimer(Timer::from_seconds(2.0, TimerMode::Repeating)));
```

## Collision Detection (Simple AABB)

```rust
#[derive(Component)]
struct Collider { half_size: Vec2 }

fn check_collisions(
    query: Query<(Entity, &Transform, &Collider)>,
    mut events: EventWriter<CollisionEvent>,
) {
    let mut combinations = query.iter_combinations();
    while let Some([(e1, t1, c1), (e2, t2, c2)]) = combinations.fetch_next() {
        let a_min = t1.translation.truncate() - c1.half_size;
        let a_max = t1.translation.truncate() + c1.half_size;
        let b_min = t2.translation.truncate() - c2.half_size;
        let b_max = t2.translation.truncate() + c2.half_size;

        if a_min.x < b_max.x && a_max.x > b_min.x
            && a_min.y < b_max.y && a_max.y > b_min.y
        {
            events.send(CollisionEvent { entity_a: e1, entity_b: e2 });
        }
    }
}
```

## Object Pooling

Reuse entities instead of spawn/despawn:

```rust
#[derive(Component)]
struct Pooled { active: bool }

fn get_from_pool(
    mut query: Query<(Entity, &mut Pooled, &mut Visibility)>,
) -> Option<Entity> {
    for (entity, mut pooled, mut vis) in &mut query {
        if !pooled.active {
            pooled.active = true;
            *vis = Visibility::Visible;
            return Some(entity);
        }
    }
    None
}

fn return_to_pool(
    entity: Entity,
    mut query: Query<(&mut Pooled, &mut Visibility)>,
) {
    if let Ok((mut pooled, mut vis)) = query.get_mut(entity) {
        pooled.active = false;
        *vis = Visibility::Hidden;
    }
}
```

## State Machines for Entities

```rust
#[derive(Component)]
enum AIState {
    Idle,
    Patrol { waypoints: Vec<Vec2>, current: usize },
    Chase { target: Entity },
    Attack { cooldown: Timer },
}

fn ai_system(
    time: Res<Time>,
    players: Query<&Transform, With<Player>>,
    mut enemies: Query<(&mut AIState, &mut Transform), With<Enemy>>,
) {
    for (mut state, mut enemy_tf) in &mut enemies {
        match state.as_mut() {
            AIState::Idle => {
                // Check for nearby player
                for player_tf in &players {
                    if enemy_tf.translation.distance(player_tf.translation) < 100.0 {
                        *state = AIState::Chase { target: Entity::PLACEHOLDER };
                    }
                }
            }
            AIState::Chase { target } => {
                // Move toward player
            }
            AIState::Attack { cooldown } => {
                cooldown.tick(time.delta());
                if cooldown.finished() {
                    // deal damage
                }
            }
            _ => {}
        }
    }
}
```

## Camera Follow

```rust
fn camera_follow(
    player: Query<&Transform, (With<Player>, Without<Camera2d>)>,
    mut camera: Query<&mut Transform, With<Camera2d>>,
    time: Res<Time>,
) {
    let Ok(player_tf) = player.get_single() else { return };
    let Ok(mut cam_tf) = camera.get_single_mut() else { return };

    let target = player_tf.translation;
    let smoothing = 5.0 * time.delta_secs();
    cam_tf.translation = cam_tf.translation.lerp(target, smoothing);
}
```

## Wave Spawning

```rust
#[derive(Resource)]
struct WaveManager {
    current_wave: u32,
    enemies_remaining: u32,
    spawn_timer: Timer,
}

fn wave_system(
    mut wave: ResMut<WaveManager>,
    mut commands: Commands,
    time: Res<Time>,
    enemies: Query<(), With<Enemy>>,
) {
    if enemies.is_empty() && wave.enemies_remaining == 0 {
        // Start next wave
        wave.current_wave += 1;
        wave.enemies_remaining = wave.current_wave * 5;
    }

    if wave.enemies_remaining > 0 {
        wave.spawn_timer.tick(time.delta());
        if wave.spawn_timer.just_finished() {
            commands.spawn((Enemy, Health(30.0 + wave.current_wave as f32 * 10.0)));
            wave.enemies_remaining -= 1;
        }
    }
}
```

## Fixed Timestep (Physics)

```rust
App::new()
    .insert_resource(Time::<Fixed>::from_hz(60.0))  // 60 FPS physics
    .add_systems(FixedUpdate, (
        apply_gravity,
        integrate_velocity,
        resolve_collisions,
    ));
```
