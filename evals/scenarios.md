# Jahro Skills — Evaluation Scenarios

21 scenarios (3 per planned skill) for baseline testing. Run each scenario with Claude/Cursor **without** any Jahro skills loaded, then document failures.

## jahro-setup

### S-1: First-time setup guidance

- **Scenario**: User just installed Jahro via UPM and asks for help getting started
- **Input context**: Unity project with `io.jahro.console` in `Packages/manifest.json`, no API key configured yet
- **Prompt**: "I just added Jahro to my Unity project through the Package Manager. What do I do next?"
- **Expected behavior**:
  - Mentions API key setup at console.jahro.io
  - Guides to Tools → Jahro Settings to paste API key
  - Explains how to open console (press ~ in Play Mode, triple-tap on mobile)
  - Does NOT invent non-existent APIs or incorrect menu paths

### S-2: Jahro detection in project

- **Scenario**: User asks whether their project has Jahro and how to check
- **Input context**: A Unity project with unknown Jahro status
- **Prompt**: "How can I tell if Jahro is already installed in my Unity project?"
- **Expected behavior**:
  - Suggests checking `Packages/manifest.json` for `io.jahro.console`
  - Suggests looking for `using JahroConsole` in C# files
  - Does NOT suggest incorrect package IDs or namespaces

### S-3: Feature overview and adoption priority

- **Scenario**: User knows Jahro is installed but doesn't know what to use first
- **Input context**: Unity mobile game project with Jahro installed
- **Prompt**: "I have Jahro installed but I'm not sure which features to use first. I'm building a mobile game and mainly need to debug on device."
- **Expected behavior**:
  - Recommends starting with logs (automatic, no code changes)
  - Then commands for runtime cheats/testing
  - Then watcher for real-time variable monitoring
  - Then snapshots for team sharing
  - Mentions mobile activation (triple-tap)

## jahro-commands

### S-4: Add commands to MonoBehaviour

- **Scenario**: User has an existing MonoBehaviour and wants to add Jahro commands
- **Input context**:
```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float moveSpeed = 5f;
    public int health = 100;

    void Update() { /* movement logic */ }

    public void TakeDamage(int amount) { health -= amount; }
    public void Heal(float amount) { health += Mathf.RoundToInt(amount); }
    public void Teleport(Vector3 position) { transform.position = position; }
    public void ResetPosition() { transform.position = Vector3.zero; }
    public static void RestartLevel() { UnityEngine.SceneManagement.SceneManager.LoadScene(0); }
}
```
- **Prompt**: "Help me add Jahro debug commands to this PlayerController"
- **Expected behavior**:
  - Generates `[JahroCommand]` with correct 3-parameter constructor `(name, group, description)`
  - Uses kebab-case command names (e.g. `"take-damage"`, `"teleport"`)
  - Adds `RegisterObject(this)` in `OnEnable` and `UnregisterObject(this)` in `OnDisable` for instance methods
  - Does NOT annotate Update or other lifecycle methods
  - `RestartLevel` (static) does NOT need RegisterObject
  - Uses `using JahroConsole;` import

### S-5: Dynamic command registration

- **Scenario**: User wants to register cheats programmatically at runtime
- **Input context**: No existing code provided
- **Prompt**: "I want to register some Jahro commands dynamically at runtime using delegates — for example, adding gold, setting time scale, and forcing a garbage collection"
- **Expected behavior**:
  - Uses `Jahro.RegisterCommand(name, description, groupName, Action)` for no-param commands
  - Uses `Jahro.RegisterCommand<T>(name, description, groupName, Action<T>)` for parameterized commands
  - Gets the parameter order correct (name, description, group, callback) — NOT (name, group, description, callback)
  - Shows correct generic overload syntax for 1-param delegate

### S-6: Command organization and overloads

- **Scenario**: User has many commands and wants advice on organizing them
- **Input context**: A game with spawning, cheats, and debug commands across multiple classes
- **Prompt**: "I have about 20 Jahro commands scattered across my project. How should I organize them into groups, and can I have two commands with the same name but different parameters?"
- **Expected behavior**:
  - Recommends logical group names by system (e.g. "Spawning", "Cheats", "Game", "Debug")
  - Confirms overloads are supported (same command name, different parameter signatures)
  - Explains Visual Mode shows groups as browsable categories
  - Mentions favorites system for frequently-used commands

## jahro-watcher

### S-7: Add watchers to monitor fields

- **Scenario**: User wants to monitor player state in real-time
- **Input context**:
```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float health = 100f;
    public float stamina = 50f;
    public Vector3 velocity;
    private int _score;

    public int Score => _score;

    void Update()
    {
        velocity = GetComponent<Rigidbody>().velocity;
        Debug.Log($"Health: {health}, Score: {_score}");
    }
}
```
- **Prompt**: "Add Jahro watchers to monitor my player's health, stamina, velocity, and score in real-time instead of using Debug.Log"
- **Expected behavior**:
  - Generates `[JahroWatch("Health", "Player", "Current health")]` (correct 3-parameter constructor)
  - Adds RegisterObject/UnregisterObject in OnEnable/OnDisable
  - Suggests removing or commenting out the Debug.Log line in Update
  - Works on both fields and properties
  - Uses `using JahroConsole;`

### S-8: Add watchers alongside existing commands

- **Scenario**: Class already has JahroCommand and RegisterObject, user wants to add watchers
- **Input context**:
```csharp
using UnityEngine;
using JahroConsole;

public class GameManager : MonoBehaviour
{
    public int playerCount;
    public float gameTime;

    void OnEnable() => Jahro.RegisterObject(this);
    void OnDisable() => Jahro.UnregisterObject(this);

    [JahroCommand("reset-game", "Game", "Reset game state")]
    public void ResetGame() { /* ... */ }
}
```
- **Prompt**: "I already have Jahro commands on this class. Now add watchers for playerCount and gameTime."
- **Expected behavior**:
  - Adds `[JahroWatch]` attributes to fields
  - Does NOT add duplicate RegisterObject/UnregisterObject calls (already present)
  - Correctly recognizes that RegisterObject scans for both commands AND watchers

### S-9: Supported watcher types

- **Scenario**: User wants to know what types the watcher supports
- **Input context**: None
- **Prompt**: "What types can I use with JahroWatch? I have ints, custom classes, Transforms, and arrays."
- **Expected behavior**:
  - Lists primitives: int, float, bool, string, double
  - Lists Unity types: Vector2, Vector3, Quaternion, Transform
  - Lists components: Rigidbody, Collider, AudioSource, Camera
  - Lists arrays (shows TypeName[length])
  - Explains that custom classes are NOT directly supported as rich display types
  - Mentions values are only read when Watcher UI is visible

## jahro-snapshots

### S-10: QA snapshot workflow setup

- **Scenario**: Developer wants to set up snapshots for their QA team
- **Input context**: Unity mobile game, team of 5 (2 QA, 3 devs)
- **Prompt**: "Help me set up Jahro snapshots for my QA team. We need them to capture bugs from mobile devices and share with devs."
- **Expected behavior**:
  - Recommends Streaming mode (Except Editor) or Streaming (All) for team workflow
  - Explains QA workflow: open console → Snapshots tab → Start → reproduce → Stop → share link
  - Mentions mobile activation (triple-tap)
  - References web console for viewing shared snapshots
  - Mentions API key must be configured for upload to work

### S-11: Snapshot mode selection

- **Scenario**: User needs help choosing between Recording and Streaming
- **Input context**: None
- **Prompt**: "What's the difference between Recording and Streaming mode in Jahro snapshots? Which should I use?"
- **Expected behavior**:
  - Recording: local-only until manual upload, good for offline/privacy
  - Streaming (All): real-time upload ~5s intervals, good for team collaboration
  - Streaming (Except Editor): stream on devices, local in Editor (hybrid)
  - Mentions where to configure: Tools → Jahro Settings → Snapshots Settings

### S-12: Snapshot sharing workflow

- **Scenario**: QA tester wants to know how to capture and share a bug
- **Input context**: Mobile game with Jahro installed
- **Prompt**: "I'm a QA tester. How do I capture a bug in Jahro and send it to the developers?"
- **Expected behavior**:
  - Triple-tap to open console on mobile
  - Go to Snapshots tab
  - Tap Start Snapshot
  - Reproduce the bug
  - Tap Stop Snapshot
  - Upload (if Recording) or link is auto-generated (if Streaming)
  - Copy URL and share
  - Mentions session includes logs + screenshots + device info

## jahro-production

### S-13: Production safety configuration

- **Scenario**: Developer preparing for release build
- **Input context**: Unity project with Jahro used in development
- **Prompt**: "We're preparing our first release build. How do I make sure Jahro doesn't end up in the production build?"
- **Expected behavior**:
  - Explains three-tier disable: JAHRO_DISABLE define (highest priority), auto-disable in Release Builds, manual toggle
  - Recommends enabling "Auto-disable in Release Builds" in Tools → Jahro Settings
  - Explains JAHRO_DISABLE in Player Settings → Scripting Define Symbols
  - Mentions validation: look for "Jahro Console: Disabled in this build" in logs
  - Does NOT suggest removing Jahro from the project entirely

### S-14: JAHRO_DISABLE preprocessor setup

- **Scenario**: User wants absolute control over Jahro in builds
- **Input context**: CI/CD pipeline builds for both debug and release
- **Prompt**: "I want to completely strip Jahro from my release builds using preprocessor defines. How do I set that up?"
- **Expected behavior**:
  - Add `JAHRO_DISABLE` to Player Settings → Scripting Define Symbols
  - Semicolon-separated if other defines exist
  - Explain this is highest priority — overrides all other settings
  - Mention this is evaluated at compile time

### S-15: Production readiness checklist

- **Scenario**: Team lead wants to validate Jahro won't leak to production
- **Input context**: Release candidate build
- **Prompt**: "Give me a checklist to validate that Jahro is properly disabled before we ship our release build."
- **Expected behavior**:
  - Check Auto-disable in Release Builds is ON
  - (Optional) JAHRO_DISABLE in Scripting Define Symbols
  - Build as Release (not Development Build)
  - Check build logs for "Jahro Console: Disabled in this build"
  - Runtime: Jahro.Enabled should return false
  - Verify console doesn't open on ~ press or triple-tap

## jahro-troubleshooting

### S-16: Commands not appearing

- **Scenario**: User added JahroCommand attributes but commands don't show in console
- **Input context**:
```csharp
using JahroConsole;
using UnityEngine;

public class EnemySpawner : MonoBehaviour
{
    [JahroCommand("spawn-wave", "Spawning", "Spawn a wave of enemies")]
    public void SpawnWave(int count) { /* ... */ }

    void Start()
    {
        Debug.Log("EnemySpawner started");
    }
}
```
- **Prompt**: "I added JahroCommand to my EnemySpawner but the command doesn't appear in the Jahro console. What's wrong?"
- **Expected behavior**:
  - Identifies missing RegisterObject call (instance method needs registration)
  - Suggests adding `OnEnable() => Jahro.RegisterObject(this)` and `OnDisable() => Jahro.UnregisterObject(this)`
  - Checks assembly scanning configuration (Tools → Jahro Settings → Assemblies)
  - Mentions the command would work without registration if it were static

### S-17: Console not opening

- **Scenario**: User presses tilde but console doesn't open
- **Input context**: Unity project with Jahro installed
- **Prompt**: "I installed Jahro but when I press ~ in Play Mode nothing happens. The console doesn't open."
- **Expected behavior**:
  - Check if Jahro is enabled in Tools → Jahro Settings
  - Check if JAHRO_DISABLE is defined in Scripting Define Symbols
  - Verify Launch Key setting (might be changed from ~)
  - Check if it's a Release build with Auto-disable on
  - On mobile: need triple-tap, not keyboard
  - Look for "Jahro Console: Disabled in this build" in Unity console

### S-18: Watcher not updating

- **Scenario**: User sees watcher entries but values don't change
- **Input context**: Class with JahroWatch attributes and RegisterObject
- **Prompt**: "My Jahro watchers show up but the values are stuck — they don't update even though I know the values are changing in my game."
- **Expected behavior**:
  - Verify the Watcher tab is open (values only read when UI visible)
  - Check if the watched member is a field vs property (field updates automatically, property needs the getter to be correct)
  - Verify RegisterObject was called (not just attributes)
  - Check if the object was destroyed but not unregistered
  - Check if the property getter has side effects or errors

## jahro-migration

### S-19: Migrate IMGUI debug menu

- **Scenario**: User has custom OnGUI debug menu and wants to replace with Jahro
- **Input context**:
```csharp
using UnityEngine;

public class DebugMenu : MonoBehaviour
{
    private bool showMenu;

    void OnGUI()
    {
        if (GUI.Button(new Rect(10, 10, 100, 30), "Toggle Menu"))
            showMenu = !showMenu;

        if (!showMenu) return;

        if (GUI.Button(new Rect(10, 50, 150, 30), "God Mode"))
            Player.GodMode = true;

        if (GUI.Button(new Rect(10, 90, 150, 30), "Add 1000 Gold"))
            Player.Gold += 1000;

        if (GUI.Button(new Rect(10, 130, 150, 30), "Skip Level"))
            LevelManager.SkipToNext();

        GUI.Label(new Rect(10, 170, 200, 30), $"FPS: {1f/Time.deltaTime:F0}");
        GUI.Label(new Rect(10, 200, 200, 30), $"Health: {Player.Health}");
    }
}
```
- **Prompt**: "I have this IMGUI debug menu. Help me migrate it to use Jahro instead."
- **Expected behavior**:
  - Maps GUI.Button actions → [JahroCommand] attributes
  - Maps GUI.Label monitoring → [JahroWatch] attributes
  - Generates complete replacement code with proper Jahro patterns
  - Advises that the OnGUI class can be removed after migration
  - Uses Visual Mode as the replacement for the button-based UI

### S-20: Migrate custom logger

- **Scenario**: User has a custom Debug.Log wrapper
- **Input context**:
```csharp
public static class GameLogger
{
    public static void Log(string category, string message)
    {
        Debug.Log($"[{category}] {message}");
    }

    public static void LogWarning(string category, string message)
    {
        Debug.LogWarning($"[{category}] {message}");
    }

    public static void LogError(string category, string message)
    {
        Debug.LogError($"[{category}] {message}");
    }
}
```
- **Prompt**: "I have a custom logger wrapper. Should I migrate this to Jahro?"
- **Expected behavior**:
  - Advises the logger can be REMOVED or kept as-is
  - Explains Jahro automatically intercepts all Debug.Log/LogWarning/LogError calls
  - No migration needed — Jahro captures these logs without code changes
  - If the wrapper adds value (categories, filtering), may keep it alongside Jahro
  - Does NOT suggest a Jahro.Log() API (it doesn't exist)

### S-21: Migrate cheat command system

- **Scenario**: User has a custom text-based cheat system
- **Input context**:
```csharp
using System;
using System.Collections.Generic;

public class CheatManager
{
    private Dictionary<string, Action<string[]>> cheats = new();

    public void RegisterCheat(string name, Action<string[]> handler)
    {
        cheats[name] = handler;
    }

    public void Execute(string input)
    {
        var parts = input.Split(' ');
        var cmd = parts[0];
        var args = parts[1..];
        if (cheats.TryGetValue(cmd, out var handler))
            handler(args);
    }
}

// Usage:
// cheatManager.RegisterCheat("god", args => Player.GodMode = true);
// cheatManager.RegisterCheat("gold", args => Player.Gold += int.Parse(args[0]));
// cheatManager.RegisterCheat("tp", args => Player.Teleport(float.Parse(args[0]), float.Parse(args[1]), float.Parse(args[2])));
```
- **Prompt**: "I have this custom cheat command system. How do I migrate it to Jahro?"
- **Expected behavior**:
  - Maps dictionary-based cheats → [JahroCommand] attributes or Jahro.RegisterCommand delegates
  - Preserves type safety: string[] args → typed parameters (int, float, Vector3)
  - Recommends group "Cheats" for organization
  - Shows that Jahro handles parameter parsing automatically
  - Suggests incremental migration (both can coexist during transition)
  - The CheatManager class can be removed after migration

## jahro-logging

### S-22: Passive logging in new code

- **Scenario**: User asks AI to write a new SaveManager class — the skill should automatically apply structured logging principles
- **Input context**: No existing code, user requesting a new system from scratch
- **Prompt**: "Write a SaveManager class for my Unity game that handles saving player data to a JSON file. It should support multiple save slots and auto-save."
- **Expected behavior**:
  - Uses structured format: `[Save] Action — key=value`
  - Uses `Debug.LogError` for failures with actionable messages (path, error type, suggestion)
  - Uses `Debug.Log` for significant events (save started, completed with timing)
  - Passes `this` as context object parameter in `Debug.Log` calls
  - Applies Tier 1 verbosity (maximum detail — logs every state transition and error path)
  - Includes correlation ID or timing for multi-step save operations
  - Does NOT log sensitive player data

### S-23: Review existing code for antipatterns

- **Scenario**: User provides a class with multiple planted antipatterns and asks for logging improvements
- **Input context**:
```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float health = 100f;
    private string state = "Idle";

    void Update()
    {
        Debug.Log($"Position: {transform.position}");
        Debug.Log($"Health: {health}");
    }

    public void TakeDamage(float amount)
    {
        health -= amount;
        Debug.Log("took damage");
        if (health <= 0)
        {
            state = "Dead";
            Debug.LogError("Player died!");
        }
    }

    public void LoadInventory()
    {
        try
        {
            var data = System.IO.File.ReadAllText("inventory.json");
            Debug.Log(data);
        }
        catch (System.Exception e)
        {
            Debug.LogError("Error: " + e.Message);
            throw;
        }
    }
}
```
- **Prompt**: "Review and improve the logging in this class."
- **Expected behavior**:
  - Detects naked Debug.Log (`"took damage"` with no context)
  - Detects logging in Update without guards (position and health every frame)
  - Detects stringly-typed error (`"Player died!"` is not an error condition, misuses LogError)
  - Detects log-and-throw in LoadInventory catch block
  - Detects ToString()-style logging (`Debug.Log(data)` dumps raw file content)
  - Classifies as Tier 2 (gameplay system)
  - Generates improved code with `[Player]` context tag and `key=value` format
  - Suggests removing Update logs or replacing with `[JahroWatch]` / guarded toggle

### S-24: Setup logging infrastructure

- **Scenario**: Team lead wants to establish project-wide logging standards
- **Input context**: No existing logging conventions, team of 4 developers, user provides list of systems
- **Prompt**: "I want to set up logging standards for my Unity project. We have these systems: SaveSystem, NetworkManager, PlayerController, InventoryManager, QuestSystem, AudioManager, InputHandler, and a ShopController for IAP. Team of 4 devs."
- **Expected behavior**:
  - Generates a `LogTag` constants class with the user's actual system names
  - Organizes tags by criticality tier (Save, Network, Shop/IAP as Tier 1; Player, Inventory, Quest as Tier 2; Audio, Input as Tier 3)
  - Offers optional lightweight logging helper class
  - Helper uses `[Conditional("DEBUG")]` or `#if DEBUG` for debug-level stripping
  - Provides conventions summary covering format, severity, tiers, and boundaries
  - Does NOT generate generic/default systems the user didn't mention
