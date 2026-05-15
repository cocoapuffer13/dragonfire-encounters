import React, { useEffect, useMemo, useState } from "react";

export default function DnDEncounterSimulator() {
  const encounters = [
    {
      name: "Goblin Ambush",
      environment: "Forest Road",
      difficulty: "Easy",
      enemies: ["4 Goblins", "1 Goblin Boss"],
      twist: "One goblin wants to defect.",
      reward: "Map to hidden ruins"
    },
    {
      name: "Dragon Negotiation",
      environment: "Volcanic Cavern",
      difficulty: "Deadly",
      enemies: ["Ancient Red Dragon"],
      twist: "The dragon offers a bargain instead of combat.",
      reward: "Legendary weapon"
    },
    {
      name: "Haunted Ballroom",
      environment: "Abandoned Castle",
      difficulty: "Medium",
      enemies: ["6 Specters", "Ghost Noble"],
      twist: "The ghosts reenact their final night every hour.",
      reward: "Family signet ring"
    },
    {
      name: "Bandit Toll Bridge",
      environment: "Stone Bridge",
      difficulty: "Easy",
      enemies: ["Bandit Captain", "8 Bandits"],
      twist: "The bandits are protecting refugees.",
      reward: "Hidden stash of silver"
    },
    {
      name: "Mind Flayer Experiment",
      environment: "Underground Lab",
      difficulty: "Hard",
      enemies: ["Mind Flayer", "2 Intellect Devourers"],
      twist: "One prisoner already carries a parasite.",
      reward: "Psionic crystal"
    }
  ];

  const monsterPools = {
    easy: [
      "Goblins",
      "Kobolds",
      "Skeletons",
      "Bandits",
      "Giant Rats",
      "Zombies",
      "Gnolls"
    ],
    medium: [
      "Ogres",
      "Wights",
      "Bugbears",
      "Werewolves",
      "Cultists",
      "Harpies",
      "Ettercaps"
    ],
    hard: [
      "Mind Flayers",
      "Young Dragons",
      "Beholders",
      "Vampires",
      "Stone Golems",
      "Frost Giants"
    ]
  };

  const environments = [
    "Emerald Forest",
    "Ancient Woodland",
    "Whispering Pines",
    "Moonlit Grove",
    "Feywild Thicket",
    "Cursed Forest",
    "Frozen Tundra",
    "Haunted Swamp",
    "Sky Fortress",
    "Ancient Ruins",
    "Underground Caverns",
    "Sunken Temple",
    "Burning Village",
    "Arcane Library",
    "Crystal Desert",
    "Storm Coast"
  ];

  const forestEncounters = [
    {
      name: "Treant Awakening",
      environment: "Ancient Woodland",
      difficulty: "Hard",
      enemies: ["Treant", "2 Vine Blights"],
      twist: "The forest itself is judging the party.",
      reward: "Heartwood Staff"
    },
    {
      name: "Fey Trickster Circle",
      environment: "Moonlit Grove",
      difficulty: "Medium",
      enemies: ["3 Satyrs", "Pixie Illusionist"],
      twist: "The party is trapped in a looping illusion.",
      reward: "Fey Charm Necklace"
    },
    {
      name: "Spider Nest Hollow",
      environment: "Whispering Pines",
      difficulty: "Hard",
      enemies: ["4 Giant Spiders", "Ettercap"],
      twist: "Webs conceal ancient ruins beneath the forest floor.",
      reward: "Venom-coated dagger"
    },
    {
      name: "The Hunter in the Mist",
      environment: "Cursed Forest",
      difficulty: "Deadly",
      enemies: ["Werewolf Stalker"],
      twist: "The hunter marks one hero as prey.",
      reward: "Moonfang Blade"
    },
    {
      name: "Dryad's Plea",
      environment: "Feywild Thicket",
      difficulty: "Easy",
      enemies: ["Corrupted Boars", "Twig Blights"],
      twist: "A dryad begs the party to save her dying tree.",
      reward: "Seed of Restoration"
    }
  ];

  const twists = [
    "A traitor is hidden among the party's allies.",
    "The enemies are cursed innocents.",
    "A powerful storm changes the battlefield every round.",
    "The encounter is secretly a test from a god.",
    "An artifact awakens mid-fight.",
    "A rival adventuring party interrupts the battle.",
    "Time freezes randomly during combat.",
    "The villain knows one hero personally."
  ];

  const rewards = [
    "Ancient gold coins",
    "Rare magical armor",
    "Potion cache",
    "Dragon egg",
    "Spellbook",
    "Enchanted relic",
    "Political favor",
    "Mysterious key"
  ];

  const difficulties = ["Easy", "Medium", "Hard", "Deadly"];

  const [selectedDifficulty, setSelectedDifficulty] = useState("All");
  const [search, setSearch] = useState("");
  const [generatedEncounter, setGeneratedEncounter] = useState(null);
  const [encounterCount, setEncounterCount] = useState(250);
  const [locationQuery, setLocationQuery] = useState("");
  const [generationDifficulty, setGenerationDifficulty] = useState(50);
  const [userLocation, setUserLocation] = useState("Unknown Region");

  useEffect(() => {
    if (typeof window !== "undefined" && navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          const latitude = position.coords.latitude.toFixed(2);
          const longitude = position.coords.longitude.toFixed(2);
          setUserLocation(`Lat ${latitude}, Long ${longitude}`);
        },
        () => {
          setUserLocation("Location Access Denied");
        }
      );
    }
  }, []);

  const allEncounters = [...encounters, ...forestEncounters];

  const filteredEncounters = useMemo(() => {
    return allEncounters.filter((encounter) => {
      const matchesDifficulty =
        selectedDifficulty === "All" ||
        encounter.difficulty === selectedDifficulty;

      const matchesSearch =
        encounter.name.toLowerCase().includes(search.toLowerCase()) ||
        encounter.environment.toLowerCase().includes(search.toLowerCase());

      const matchesLocation =
        locationQuery.trim() === "" ||
        encounter.environment
          .toLowerCase()
          .includes(locationQuery.toLowerCase());

      return matchesDifficulty && matchesSearch && matchesLocation;
    });
  }, [selectedDifficulty, search, locationQuery]);

  const randomItem = (array) => {
    return array[Math.floor(Math.random() * array.length)];
  };

  const generateEncounter = () => {
    let difficulty = "Medium";

    if (generationDifficulty <= 25) {
      difficulty = "Easy";
    } else if (generationDifficulty <= 60) {
      difficulty = "Medium";
    } else if (generationDifficulty <= 85) {
      difficulty = "Hard";
    } else {
      difficulty = "Deadly";
    }

    const normalizedLocation = locationQuery.toLowerCase();

    let themedEnvironments = [...environments];

    const themedPools = {
      Easy: [
        "Goblin Scouts",
        "Bandit Rangers",
        "Kobolds",
        "Twig Blights",
        "Giant Rats"
      ],
      Medium: [
        "Dire Wolves",
        "Owlbears",
        "Ettercaps",
        "Cultists",
        "Werewolves"
      ],
      Hard: [
        "Treants",
        "Hydras",
        "Stone Golems",
        "Vampires",
        "Mind Flayers"
      ],
      Deadly: [
        "Ancient Dragons",
        "Liches",
        "Beholders",
        "Balors",
        "Frost Giants"
      ]
    };

    let themedEnemies = themedPools[difficulty];

    if (
      normalizedLocation.includes("forest") ||
      normalizedLocation.includes("woods") ||
      normalizedLocation.includes("jungle")
    ) {
      themedEnvironments = [
        "Emerald Forest",
        "Ancient Woodland",
        "Whispering Pines",
        "Moonlit Grove",
        "Feywild Thicket",
        "Cursed Forest"
      ];

      themedEnemies = {
        Easy: ["Bandit Rangers", "Twig Blights", "Wolf Pack"],
        Medium: ["Dire Wolves", "Owlbears", "Giant Spiders"],
        Hard: ["Treants", "Werewolf Hunters", "Forest Hydras"],
        Deadly: [
          "Ancient Green Dragon",
          "Archfey Guardian",
          "Corrupted Treant Titan"
        ]
      }[difficulty];
    } else if (normalizedLocation.includes("swamp")) {
      themedEnvironments = ["Haunted Swamp", "Sunken Temple"];

      themedEnemies = {
        Easy: ["Swamp Zombies", "Giant Toads"],
        Medium: ["Lizardfolk", "Harpies"],
        Hard: ["Hydras", "Swamp Trolls"],
        Deadly: ["Black Dragon", "Ancient Hydra"]
      }[difficulty];
    } else if (
      normalizedLocation.includes("ice") ||
      normalizedLocation.includes("snow") ||
      normalizedLocation.includes("frozen")
    ) {
      themedEnvironments = ["Frozen Tundra", "Storm Coast"];

      themedEnemies = {
        Easy: ["Ice Mephits", "Winter Wolves"],
        Medium: ["Frost Druids", "Polar Owlbears"],
        Hard: ["Frost Giants", "Remorhaz"],
        Deadly: ["Ancient White Dragon", "Titan of Ice"]
      }[difficulty];
    } else if (normalizedLocation.includes("desert")) {
      themedEnvironments = ["Crystal Desert"];

      themedEnemies = {
        Easy: ["Sand Bandits", "Scorpions"],
        Medium: ["Mummies", "Dust Mephits"],
        Hard: ["Sand Wyrms", "Phoenix Cultists"],
        Deadly: ["Ancient Blue Dragon", "Desert Titan"]
      }[difficulty];
    }

    const selectedEnemy = randomItem(themedEnemies);

    const encounterTitles = {
      "Goblin Scouts": "Goblin Ambush",
      "Bandit Rangers": "Forest Outlaw Raid",
      Kobolds: "Kobold Tunnel Assault",
      "Twig Blights": "Corrupted Grove",
      "Giant Rats": "Sewer Beast Swarm",
      "Dire Wolves": "Moonfang Hunt",
      Owlbears: "Owlbear Rampage",
      Ettercaps: "Webbed Forest Trap",
      Cultists: "Forbidden Ritual",
      Werewolves: "Curse of the Full Moon",
      Treants: "Wrath of the Ancient Forest",
      Hydras: "Hydra Marsh Terror",
      "Stone Golems": "Ruins Guardian Awakening",
      Vampires: "Crimson Castle Hunt",
      "Mind Flayers": "Psionic Nightmare",
      "Ancient Dragons": "Ancient Dragon Encounter",
      Liches: "The Lich King's Return",
      Beholders: "Eye Tyrant's Domain",
      Balors: "Infernal Rift Catastrophe",
      "Frost Giants": "Frozen Giant Siege",
      "Ancient Green Dragon": "Emerald Dragon's Wrath",
      "Archfey Guardian": "Fey Court Judgment",
      "Corrupted Treant Titan": "Titan of the Cursed Woods",
      "Black Dragon": "Black Dragon Swamp Terror",
      "Ancient Hydra": "Hydra of the Drowned Temple",
      "Ancient White Dragon": "White Dragon Blizzard",
      "Titan of Ice": "Titan Beneath the Glacier",
      "Ancient Blue Dragon": "Storm Dragon Awakening",
      "Desert Titan": "Wrath of the Crystal Desert"
    };

    const enemyCount =
      difficulty === "Easy"
        ? Math.floor(Math.random() * 4) + 2
        : difficulty === "Medium"
        ? Math.floor(Math.random() * 3) + 2
        : difficulty === "Hard"
        ? Math.floor(Math.random() * 2) + 1
        : 1;

    const generated = {
      name: encounterTitles[selectedEnemy] || `${selectedEnemy} Encounter`,
      environment: randomItem(themedEnvironments),
      difficulty,
      enemies: [`${enemyCount} ${selectedEnemy}`],
      twist: randomItem(twists),
      reward: randomItem(rewards),
      challengeRating: generationDifficulty,
      locationTheme:
        locationQuery.trim() === ""
          ? "Generic Wilderness"
          : locationQuery
    };

    setGeneratedEncounter(generated);
  };

  const massiveSimulation = useMemo(() => {
    const sims = [];

    for (let i = 0; i < encounterCount; i++) {
      const difficulty = randomItem(difficulties);

      const enemyPool =
        difficulty === "Easy"
          ? monsterPools.easy
          : difficulty === "Medium"
          ? monsterPools.medium
          : monsterPools.hard;

      sims.push({
        id: i + 1,
        name: `${randomItem(enemyPool)} Skirmish`,
        difficulty,
        environment: randomItem(environments),
        twist: randomItem(twists)
      });
    }

    return sims;
  }, [encounterCount]);

  const difficultyColor = (difficulty) => {
    switch (difficulty) {
      case "Easy":
        return "bg-green-600";
      case "Medium":
        return "bg-yellow-600";
      case "Hard":
        return "bg-orange-600";
      case "Deadly":
        return "bg-red-700";
      default:
        return "bg-gray-600";
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-black via-red-950 to-amber-950 text-white p-6">
      <div className="max-w-7xl mx-auto space-y-8">
        <div className="text-center space-y-4">
          <div className="flex flex-col items-center gap-4">
            <div className="text-7xl animate-pulse">🐲</div>
            <h1 className="text-5xl md:text-6xl font-black tracking-tight text-transparent bg-clip-text bg-gradient-to-r from-red-400 via-orange-300 to-yellow-200">
            🐉 Dragonfire Encounter Simulator
          </h1>
          </div>

          <p className="text-lg text-amber-200 max-w-3xl mx-auto">
            Forge legendary dragon-themed encounters, ancient ruins, cursed forests, and deadly adventures worthy of epic heroes.
          </p>
        </div>

        <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
          <div className="bg-red-950/40 backdrop-blur-md rounded-[2rem] p-6 shadow-[0_0_30px_rgba(255,100,0,0.25)] border border-amber-700/30 space-y-5">
            <h2 className="text-2xl font-bold">Encounter Controls</h2>

            <div className="space-y-2">
              <label className="text-sm text-amber-200">Search</label>
              <input
                value={search}
                onChange={(e) => setSearch(e.target.value)}
                placeholder="Search encounters..."
                className="w-full rounded-xl bg-black/60 border border-amber-700/30 px-4 py-3 focus:outline-none focus:ring-2 focus:ring-red-500"
              />
            </div>

            <div className="space-y-2">
              <label className="text-sm text-amber-200">
                Search by Location / Environment
              </label>
              <input
                value={locationQuery}
                onChange={(e) => setLocationQuery(e.target.value)}
                placeholder="Example: swamp, ruins, coast..."
                className="w-full rounded-xl bg-black/60 border border-amber-700/30 px-4 py-3 focus:outline-none focus:ring-2 focus:ring-cyan-500"
              />
              <div className="text-xs text-amber-400">
                Current User Location: {userLocation}
              </div>
            </div>

            <div className="space-y-2">
              <label className="text-sm text-amber-200">Difficulty</label>
              <select
                value={selectedDifficulty}
                onChange={(e) => setSelectedDifficulty(e.target.value)}
                className="w-full rounded-xl bg-black/60 border border-amber-700/30 px-4 py-3"
              >
                <option value="All">All</option>
                {difficulties.map((difficulty) => (
                  <option key={difficulty} value={difficulty}>
                    {difficulty}
                  </option>
                ))}
              </select>
            </div>

            <div className="space-y-2">
              <label className="text-sm text-amber-200">
                Massive Simulation Count
              </label>
              <input
                type="range"
                min="50"
                max="1000"
                step="50"
                value={encounterCount}
                onChange={(e) => setEncounterCount(Number(e.target.value))}
                className="w-full"
              />
              <div className="text-sm text-amber-400">
                Simulating: {encounterCount} encounters
              </div>
            </div>

            <div className="space-y-3">
              <div className="flex items-center justify-between">
                <label className="text-sm text-amber-200 font-semibold">
                  Dragon Threat Level
                </label>
                <span className="text-amber-400 font-bold">
                  {generationDifficulty}
                </span>
              </div>

              <input
                type="range"
                min="1"
                max="100"
                value={generationDifficulty}
                onChange={(e) =>
                  setGenerationDifficulty(Number(e.target.value))
                }
                className="w-full accent-red-600"
              />

              <div className="flex justify-between text-xs text-amber-500">
                <span>Peasant Patrol</span>
                <span>Dragon Slayer</span>
                <span>Apocalypse</span>
              </div>
            </div>

            <button
              onClick={generateEncounter}
              className="w-full bg-gradient-to-r from-red-700 via-orange-600 to-amber-500 hover:scale-[1.02] transition rounded-[1.5rem] py-4 text-lg font-bold shadow-lg"
            >
              Generate Random Encounter
            </button>
          </div>

          <div className="lg:col-span-2 bg-red-950/40 backdrop-blur-md rounded-[2rem] p-6 shadow-[0_0_30px_rgba(255,100,0,0.25)] border border-amber-700/30">
            <h2 className="text-2xl font-bold mb-5">Featured Encounters</h2>

            {filteredEncounters.length === 0 ? (
              <div className="text-amber-400 text-center py-12">
                No encounters match your search.
              </div>
            ) : (
              <div className="grid md:grid-cols-2 gap-5">
                {filteredEncounters.map((encounter, index) => (
                  <div
                    key={`${encounter.name}-${index}`}
                    className="bg-gradient-to-br from-black/60 to-red-950/40 rounded-[1.5rem] p-5 border border-amber-700/30 hover:scale-[1.02] transition"
                  >
                    <div className="flex items-center justify-between mb-3">
                      <h3 className="text-xl font-bold">{encounter.name}</h3>
                      <span
                        className={`px-3 py-1 rounded-full text-sm font-semibold ${difficultyColor(
                          encounter.difficulty
                        )}`}
                      >
                        {encounter.difficulty}
                      </span>
                    </div>

                    <div className="space-y-2 text-amber-200 text-sm">
                      <p>
                        <span className="font-semibold text-white">
                          Environment:
                        </span>{" "}
                        {encounter.environment}
                      </p>
                      <p>
                        <span className="font-semibold text-white">
                          Enemies:
                        </span>{" "}
                        {encounter.enemies.join(", ")}
                      </p>
                      <p>
                        <span className="font-semibold text-white">
                          Twist:
                        </span>{" "}
                        {encounter.twist}
                      </p>
                      <p>
                        <span className="font-semibold text-white">
                          Reward:
                        </span>{" "}
                        {encounter.reward}
                      </p>
                    </div>
                  </div>
                ))}
              </div>
            )}
          </div>
        </div>

        {generatedEncounter && (
          <div className="bg-gradient-to-r from-red-950/70 via-orange-900/40 to-amber-700/20 rounded-[2rem] p-8 border border-amber-700/30 shadow-[0_0_30px_rgba(255,100,0,0.25)]">
            <div className="flex flex-wrap items-center gap-4 mb-5">
              <h2 className="text-3xl font-black">
                {generatedEncounter.name}
              </h2>
              <span
                className={`px-4 py-2 rounded-full font-bold ${difficultyColor(
                  generatedEncounter.difficulty
                )}`}
              >
                {generatedEncounter.difficulty}
              </span>
            </div>

            <div className="grid md:grid-cols-2 gap-6 text-slate-200">
              <div>
                <h3 className="font-bold text-white mb-2">Environment</h3>
                <p>{generatedEncounter.environment}</p>
              </div>

              <div>
                <h3 className="font-bold text-white mb-2">Enemies</h3>
                <p>{generatedEncounter.enemies.join(", ")}</p>
              </div>

              <div>
                <h3 className="font-bold text-white mb-2">Encounter Twist</h3>
                <p>{generatedEncounter.twist}</p>
              </div>

              <div>
                <h3 className="font-bold text-white mb-2">
                  Threat Level
                </h3>
                <p>{generatedEncounter.challengeRating}/100</p>
              </div>

              <div>
                <h3 className="font-bold text-white mb-2">Reward</h3>
                <p>{generatedEncounter.reward}</p>
              </div>
            </div>
          </div>
        )}

        <div className="bg-red-950/40 backdrop-blur-md rounded-[2rem] p-6 border border-amber-700/30 shadow-[0_0_30px_rgba(255,100,0,0.25)]">
          <div className="flex flex-wrap items-center justify-between gap-4 mb-6">
            <div>
              <h2 className="text-2xl font-bold">
                Massive Encounter Simulation
              </h2>
              <p className="text-amber-200">
                Procedurally generated encounter previews.
              </p>
            </div>

            <div className="bg-gradient-to-br from-black/60 to-red-950/40 rounded-[1.5rem] px-5 py-3 text-lg font-bold">
              {massiveSimulation.length} Encounters Simulated
            </div>
          </div>

          <div className="max-h-[500px] overflow-y-auto rounded-[1.5rem] border border-amber-700/30">
            <table className="w-full text-left">
              <thead className="sticky top-0 bg-black/70 backdrop-blur-md">
                <tr>
                  <th className="p-4">#</th>
                  <th className="p-4">Encounter</th>
                  <th className="p-4">Difficulty</th>
                  <th className="p-4">Environment</th>
                  <th className="p-4">Twist</th>
                </tr>
              </thead>

              <tbody>
                {massiveSimulation.map((sim) => (
                  <tr
                    key={sim.id}
                    className="border-t border-white/5 hover:bg-red-900/20 transition"
                  >
                    <td className="p-4 text-amber-400">{sim.id}</td>
                    <td className="p-4 font-semibold">{sim.name}</td>
                    <td className="p-4">
                      <span
                        className={`px-3 py-1 rounded-full text-sm ${difficultyColor(
                          sim.difficulty
                        )}`}
                      >
                        {sim.difficulty}
                      </span>
                    </td>
                    <td className="p-4 text-amber-200">{sim.environment}</td>
                    <td className="p-4 text-amber-400">{sim.twist}</td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </div>

        <div className="text-center text-slate-500 text-sm pt-2">
          🔥 The dragons are watching your campaign unfold. 🔥
        </div>
      </div>
    </div>
  );
}

