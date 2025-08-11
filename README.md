<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Doskvol Rumor Generator</title>
    <!-- Load Tailwind CSS from CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@700&family=Inter:wght@400;700&display=swap');
        
        body {
            font-family: 'Inter', sans-serif;
            background-color: #161c28; /* A darker, desaturated blue-gray for a moody feel */
            color: #e2e8f0; /* Light gray text */
        }
        
        h1 {
            font-family: 'Cormorant Garamond', serif;
        }

        .rumor-item {
            @apply p-4 bg-[#1f2937] border border-[#2d3a4b] rounded-lg mb-4 italic transition-all duration-300;
        }
        .rumor-item:last-child {
            margin-bottom: 0;
        }
        .loading-animation {
            border: 4px solid rgba(255, 255, 255, 0.2);
            border-top: 4px solid #fff;
            border-radius: 50%;
            width: 24px;
            height: 24px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen p-4">
    <!-- Confirmation Modal -->
    <div id="clearConfirmModal" class="fixed inset-0 bg-gray-900 bg-opacity-75 flex items-center justify-center z-50 hidden">
        <div class="bg-gray-800 p-8 rounded-xl shadow-2xl max-w-sm w-full text-center">
            <h3 class="text-xl font-bold mb-4">Are you sure?</h3>
            <p class="text-gray-300 mb-6">This will permanently delete all your saved rumors.</p>
            <div class="flex space-x-4">
                <button id="confirmClear" class="flex-1 bg-red-600 hover:bg-red-700 text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-opacity-50">
                    Yes, Clear All
                </button>
                <button id="cancelClear" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-gray-400 focus:ring-opacity-50">
                    Cancel
                </button>
            </div>
        </div>
    </div>
    
    <div class="container mx-auto p-8 bg-[#1f2937] rounded-xl shadow-lg border border-[#2d3a4b]">

        <!-- User ID Display for Collaboration/Persistence -->
        <div class="mb-6 text-center text-sm text-gray-400">
            <p>Your User ID: <span id="userId" class="font-mono text-xs bg-[#2d3a4b] rounded px-2 py-1 select-all">Loading...</span></p>
        </div>

        <h1 class="text-4xl md:text-5xl font-bold text-center mb-6 text-[#D4AF37]">Doskvol Rumor Generator</h1>
        <p class="text-center text-lg mb-8 text-gray-300">Generate fresh rumors and plot hooks for your <strong>Blades in the Dark</strong> campaign.</p>
        
        <!-- Rumor Display Area -->
        <div id="rumorDisplay" class="min-h-[150px] transition-all duration-300">
            <div class="p-6 bg-[#161c28] border border-[#2d3a4b] rounded-lg text-center text-lg md:text-xl italic">
                <p id="initialMessage">Loading rumors...</p>
                <div id="loadingIndicator" class="mx-auto loading-animation"></div>
            </div>
        </div>

        <!-- Buttons for single generation -->
        <div class="mt-8">
            <div class="flex flex-col md:flex-row space-y-4 md:space-y-0 md:space-x-4 mb-4">
                <button id="generateGeneral" class="flex-1 bg-[#b78c43] hover:bg-[#a07a3c] text-black font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-[#D4AF37] focus:ring-opacity-50">
                    General Rumor
                </button>
                <button id="generateFaction" class="flex-1 bg-[#7d4f50] hover:bg-[#6c4445] text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-[#7d4f50] focus:ring-opacity-50">
                    Faction Rumor
                </button>
                <button id="generateCharacter" class="flex-1 bg-[#4a586e] hover:bg-[#3f4a5f] text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-[#4a586e] focus:ring-opacity-50">
                    Character Rumor
                </button>
                <button id="generateDistrict" class="flex-1 bg-[#4f7d6a] hover:bg-[#456d5b] text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-[#4f7d6a] focus:ring-opacity-50">
                    District Rumor
                </button>
            </div>
            
            <div class="flex flex-col md:flex-row space-y-4 md:space-y-0 md:space-x-4 mb-4">
                <button id="generateItem" class="flex-1 bg-[#6c483a] hover:bg-[#5b3c31] text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-[#6c483a] focus:ring-opacity-50">
                    Item Rumor
                </button>
                <button id="generateBuilding" class="flex-1 bg-[#734a66] hover:bg-[#624056] text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-[#734a66] focus:ring-opacity-50">
                    Building Rumor
                </button>
                <button id="generateVice" class="flex-1 bg-[#5d3f6a] hover:bg-[#4e3458] text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-[#5d3f6a] focus:ring-opacity-50">
                    Vice Rumor
                </button>
                <button id="generateCatalyst" class="flex-1 bg-teal-600 hover:bg-teal-700 text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-teal-500 focus:ring-opacity-50 hidden">
                    Catalyst Rumor
                </button>
            </div>
            
            <!-- Batch Generation Controls -->
            <div class="bg-[#1f2937] p-6 rounded-lg shadow-inner">
                <h3 class="text-xl font-bold mb-4 text-gray-200">Batch Generation</h3>
                <div class="flex flex-col sm:flex-row sm:flex-wrap gap-4 mb-4">
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="includeGeneral" class="rumor-type-checkbox rounded text-[#D4AF37] bg-[#2d3a4b] border-[#4a586e] focus:ring-[#D4AF37]" checked>
                        <label for="includeGeneral" class="text-sm">General</label>
                    </div>
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="includeFaction" class="rumor-type-checkbox rounded text-[#7d4f50] bg-[#2d3a4b] border-[#4a586e] focus:ring-[#7d4f50]" checked>
                        <label for="includeFaction" class="text-sm">Faction</label>
                    </div>
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="includeCharacter" class="rumor-type-checkbox rounded text-[#4a586e] bg-[#2d3a4b] border-[#4a586e] focus:ring-[#4a586e]" checked>
                        <label for="includeCharacter" class="text-sm">Character</label>
                    </div>
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="includeDistrict" class="rumor-type-checkbox rounded text-[#4f7d6a] bg-[#2d3a4b] border-[#4a586e] focus:ring-[#4f7d6a]" checked>
                        <label for="includeDistrict" class="text-sm">District</label>
                    </div>
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="includeItem" class="rumor-type-checkbox rounded text-[#6c483a] bg-[#2d3a4b] border-[#4a586e] focus:ring-[#6c483a]" checked>
                        <label for="includeItem" class="text-sm">Item</label>
                    </div>
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="includeBuilding" class="rumor-type-checkbox rounded text-[#734a66] bg-[#2d3a4b] border-[#4a586e] focus:ring-[#734a66]" checked>
                        <label for="includeBuilding" class="text-sm">Building</label>
                    </div>
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="includeVice" class="rumor-type-checkbox rounded text-[#5d3f6a] bg-[#2d3a4b] border-[#4a586e] focus:ring-[#5d3f6a]" checked>
                        <label for="includeVice" class="text-sm">Vice</label>
                    </div>
                    <div class="flex items-center space-x-2 hidden" id="catalystCheckboxContainer">
                        <input type="checkbox" id="includeCatalyst" class="rumor-type-checkbox rounded text-teal-500 bg-[#2d3a4b] border-[#4a586e] focus:ring-teal-500">
                        <label for="includeCatalyst" class="text-sm">Catalyst</label>
                    </div>
                </div>
                <div class="flex items-center justify-between space-x-4">
                    <div class="flex items-center space-x-2">
                        <input type="number" id="batchSize" value="5" min="1" max="100" class="w-20 bg-[#2d3a4b] text-white p-2 rounded-lg border border-[#4a586e] focus:ring-2 focus:ring-[#D4AF37] focus:outline-none">
                        <label for="batchSize" class="text-sm"># of rumors</label>
                    </div>
                    <button id="generateBatch" class="flex-1 bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:ring-opacity-50">
                        Generate Batch
                    </button>
                    <button id="generateHundred" class="bg-gray-500 hover:bg-gray-600 text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-gray-400 focus:ring-opacity-50">
                        Generate 100
                    </button>
                </div>
            </div>
            
            <!-- Clear All and Copy Buttons -->
            <div class="mt-8 text-center flex flex-col sm:flex-row space-y-4 sm:space-y-0 sm:space-x-4 justify-center">
                <button id="clearAllBtn" class="flex-1 bg-red-800 hover:bg-red-900 text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-opacity-50">
                    Clear All Rumors
                </button>
                <button id="copyAllBtn" class="flex-1 bg-[#1a646c] hover:bg-[#1a555a] text-white font-bold py-3 px-6 rounded-lg shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-[#1a646c] focus:ring-opacity-50">
                    Copy All Rumors
                </button>
            </div>
        </div>
        <!-- Deep Cuts Toggle -->
        <div class="flex justify-center mt-6">
            <div class="flex items-center space-x-2">
                <input type="checkbox" id="deepCutsToggle" class="rounded text-[#4f7d6a] bg-[#2d3a4b] border-[#4a586e] focus:ring-[#4f7d6a]" checked>
                <label for="deepCutsToggle" class="text-sm text-gray-400">Include Deep Cuts Content</label>
            </div>
        </div>
    </div>

    <!-- JavaScript for generation logic -->
    <script type="module">
        // Import Firebase modules
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, addDoc, onSnapshot, collection, query, serverTimestamp, getDocs, deleteDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Firebase Initialization
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        let db, auth, userId;

        // IIFE for initialization
        (async () => {
            try {
                const app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Sign in with custom token or anonymously
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }

                // Wait for auth state to be ready
                onAuthStateChanged(auth, (user) => {
                    if (user) {
                        userId = user.uid;
                        document.getElementById('userId').textContent = userId;

                        // Set up real-time listener for rumors
                        setupRumorListener();
                    } else {
                        console.error("User not authenticated.");
                    }
                });

            } catch (error) {
                console.error("Firebase initialization failed:", error);
                document.getElementById('initialMessage').textContent = "Failed to load. Please try again.";
                document.getElementById('loadingIndicator').style.display = 'none';
            }
        })();

        // Data for the Generator
        const coreData = {
            factions: ["The Unseen", "Foundation", "The Circle of Flame", "The Lampblacks", "The Dimmer Sisters", "The Billhooks", "The Gray Cloaks", "The Fog Hounds", "Council", "Spirit Wardens", "Imperial Military", "Sparkwrights", "The Lost", "Skovlander Refugees", "The Consulate of Iruvia", "The Consulate of The Dagger Isles", "Ecstasy of the Flesh", "Ulf Ironborn", "The Crows", "The Red Sashes", "The Silver Nails", "The Grinders", "The Forgotten Gods"],
            namedNpcs: ["Lord Scurlock", "Mardin Gull", "Pux Bolin", "Helene", "Harvale Brogan", "Freyla", "Avrick", "Rolan Volaris", "Madame Tesslyn", "Helles a-Roden", "Slane Karstas", "Carro Skelkallan", "Lord Governor", "Lyssa", "Bell", "Baz", "Vance", "Sekereth", "Ring", "Remira"],
            namePrefixes: ["al", "ar", "ad", "br", "bo", "cy", "da", "di", "el", "es", "ev", "fe", "fa", "ga", "go", "he", "ir", "is", "ka", "ke", "la", "le", "li", "ma", "me", "na", "ne", "ni", "or", "os", "pe", "ra", "re", "sa", "se", "sh", "sk", "ta", "te", "to", "ul", "va", "ve", "za", "ze"],
            nameSuffixes: ["en", "or", "an", "on", "ia", "ian", "os", "us", "er", "es", "in", "ith", "o", "a", "as", "lyn", "ana", "ara", "oan", "an", "un", "ell"],
            districts: ["White Crown", "Brightstone", "Charterhall", "Six Towers", "Silkshore", "Nightmarket", "Crow's Foot", "The Docks", "Barrowcleft", "The Lost District", "Coalridge", "Ironhook Prison", "Dunslough", "Charhollow"],
            moves: ["smuggle", "escort", "deliver", "transport", "ferry", "export", "push", "haul", "pay", "give", "take", "bring", "rob", "steal", "kidnap", "sabotage"],
            professions: ["courtier", "counselor", "stevedore", "pilgrim", "diplomat", "eel farmer", "sailor", "pastry cook", "sportsman", "loremaster", "bookbinder", "scout", "ferryman", "water carrier", "postman", "musician", "cartographer", "princess", "bottler", "wetnurse", "pysicker", "alchemist", "shipwright", "spirit dealer", "ghost wrangler", "spirit hunter", "soldier", "guardsman", "bodyguard", "lancer", "whisper", "cutter", "leech", "spider", "hound", "slide", "hack"],
            warriors: ["dualist", "brute", "knifer", "dagger master", "skulking blade", "cutter", "mercenary", "guard", "Bluecoat", "Akrosi lancer", "brawler", "thug", "pistoleer", "assassin", "veteran"],
            itemAdjectives: ["hexworked", "electoplasmic", "haunted", "gleaming", "rusty", "whispering", "ghost-touched", "corrupted", "brittle", "ancient", "glowing", "forbidden", "tarnished", "carved", "shadow-forged", "arcane", "oiled", "polished", "cracked"],
            itemNouns: ["dagger", "hammer", "locket", "tome", "key", "crystal", "lantern", "pistol", "watch", "ring", "skull", "lockpick", "glove", "mask", "pipe", "compass", "map", "coin", "relic"],
            items: ["stolen goods", "valuable treasures", "contraband", "large collection of valuables", "dark secret", "secret artifact", "ancient artifacts", "rare electroplasm", "pistol parts", "illegal documents"],
            vehicles: ["a goat-drawn carriage", "a goat-drawn cart", "a gondola", "a steam carriage", "an Iruvian horse", "a penny-farthing bike"],
            looks: ["large", "slim", "bony", "short", "cute", "disfigured", "lovely", "dark", "worn", "tall", "plain", "glasses-wearing", "weathered", "rough", "sexy", "augmented", "chiseled", "plump", "wild", "long-haired", "handsome", "stout", "young", "bearded", "athletic", "delicate", "wiry", "elegant", "stylish", "shaven", "tattooed", "scarred", "striking", "stooped", "strange"],
            goals: ["wealth", "power", "authority", "prestige", "fame", "control", "knowledge", "pleasure", "revenge", "freedom", "achievement", "happiness", "infamy", "fear", "respect", "love", "chaos", "destruction", "justice", "cooperation"],
            buildingTypes: {
                "general": ["inn", "tavern", "gambling hall", "drug den", "brothel", "market", "workshop", "bakery", "butchery", "forge", "tailory", "goat stables", "watch post", "court", "jail", "dock", "tenements", "apartment building", "small house", "bath house", "shrine", "tattoist", "physicker", "fighting pits", "square", "fountain", "grotto", "warehouse", "stockyard", "factory", "refinery", "eelery", "mushroom garden", "restaurant", "bar", "lounge", "academy", "salon", "cafe", "apothecary", "book shop", "jeweler", "clothier", "gallery", "theater", "opera house", "apartment building", "townhouse", "manor house", "villa", "temple", "cistern", "watch post", "park", "monument", "bank", "alchemist", "power plant", "radiant energy garden"],
                "White Crown": ["academy", "gallery", "manor house", "theater", "jeweler", "bank"],
                "Brightstone": ["bank", "cafe", "restaurant", "jeweler", "gallery", "opera house", "theater"],
                "Charterhall": ["court", "jail", "bank", "academy", "fountain", "park", "cafe", "gallery", "temple"],
                "Six Towers": ["inn", "tavern", "gambling hall", "apartment building", "small house", "shrine", "restaurant", "bar"],
                "Silkshore": ["brothel", "tavern", "drug den", "market", "restaurant", "cafe", "lounge", "theater", "gondoliers"],
                "Nightmarket": ["market", "workshop", "restaurant", "bar", "lounge", "inn", "gambling hall", "apartment building", "townhouse"],
                "Crow's Foot": ["inn", "tavern", "brothel", "market", "workshop", "butchery", "forge", "tailory", "watch post", "court", "jail", "dock", "tenements", "apartment building", "small house", "bath house", "shrine"],
                "The Docks": ["inn", "tavern", "brothel", "market", "workshop", "butchery", "forge", "tailory", "goat stables", "watch post", "court", "jail", "dock", "tenements", "apartment building", "small house", "bath house", "shrine"],
                "Barrowcleft": ["market", "workshop", "bakery", "butchery", "forge", "tailory", "goat stables", "watch post", "dock", "tenements", "apartment building", "small house", "farm"],
                "Coalridge": ["factory", "refinery", "warehouse", "stockyard", "mushroom garden", "inn", "tavern", "apartment building", "small house", "bath house", "shrine"],
                "Charhollow": ["inn", "tavern", "gambling hall", "drug den", "brothel", "market", "workshop", "bakery", "butchery", "forge", "tailory", "goat stables", "watch post", "court", "jail", "dock", "tenements", "apartment building", "small house", "bath house", "shrine"],
                "Dunslough": ["jail", "court", "workhouse", "labor camp", "refinery", "warehouse", "stockyard", "factory"]
            },
            buildingDetails: ["dripping water", "creaking floorboards", "roaring fires", "smoky lamps", "buzzing electric lights", "ticking clockwork", "plants", "flowers", "wall hangings", "shuttered windows", "heavy curtains", "thick carpet", "dust", "detritus", "wear", "damage", "threadbare", "tattered", "utilitarian furnishings", "elegant finery", "lush", "comfortable", "rough", "spun", "simplicity", "spartan austerity", "circular stairs", "ladders", "secret doors", "catwalks", "skylights", "balcony", "cellar", "drafty", "cold", "stout", "cozy", "warm", "vaulted", "spacious", "low", "cramped", "rickety", "ramshackle"],
            buildingItems: ["chalkboard", "desks", "papers", "maps", "charts", "diagrams", "books", "scrolls", "bookcases", "lamp", "inkwell", "writing desk", "clock", "cabinet", "shelves", "table", "chairs", "notebooks", "bed", "bureau", "vanity", "bunks", "stools", "barrels", "pitcher", "mirror", "sofa", "divan", "music box", "couch", "table", "lamps", "drapery", "pillows", "cushions", "counter", "sink", "cabinet", "cookfire", "pots", "pans", "utensils", "dining table", "chairs", "gallery", "game board", "cards", "dice", "larder", "cheeses", "wine", "beer", "whiskey", "pedestal", "statue", "paintings", "bird cage", "quill", "diary", "bell", "book", "chain", "fireplace", "rug", "armchair", "curtains", "vases", "flowers", "instruments", "music sheets", "exam chair", "medical tools", "barrels", "vials", "flowers", "workbench", "tools", "rags", "weapons", "ammunition"],
            vicePurveyors: {"Mardin Gull": "The Leaky Bucket", "Pux Bolin": "The Harping Monkey", "Helene": "The Silver Stag casino", "Lady Freyla": "The Emperor's Cask", "Avrick": "Barrowcleft", "Rolan Volaris": "The Veil social club", "Madame Tesslyn": "The Red Lamp brothel", "Eldrin Prichard": "The Silver Swan pleasure barge", "Spogg": "Crow's Foot", "Grist": "The Docks", "Master Vreen": "Nightmarket", "Lady Dusk": "Whitecrown", "Sergeant Velk": "Dunslough", "Chief Roselle": "Six Towers", "Mother Narya": "Six Towers", "Hutton": "Charhollow", "The Church of Ecstasy": "Brightstone", "Ilacille": "Coalridge", "Lord Penderyn": "Charterhall", "Dunridge & Sons": "Nightmarket", "Maestro Helleren": "Whitecrown"},
            viceLocations: {"The Leaky Bucket": "Crow's Foot", "The Harping Monkey": "Nightmarket", "The Silver Stag casino": "Silkshore", "The Emperor's Cask": "Whitecrown", "The Veil social club": "Nightmarket", "The Red Lamp brothel": "Silkshore", "The Silver Swan pleasure barge": "Brightstone", "The fighting pits": "Dunslough", "Dusk Manor Club": "Whitecrown", "Spiregarden Theater": "Whitecrown", "Centuralia Club": "Brightstone", "Gaddoc Station": "The Docks", "Traven's smoke shop": "Coalridge"},
            generalRumors: ["Word on the street is that a well-known {profession} named {generic_name} wants the {faction} to {move} valuable items to {district}.", "An old {profession} named {generic_name} has been asking about {named_npc} in {district}. They say {named_npc} has a dark secret.", "A {warrior} challenges a member of the crew to a duel in {district}.", "A whisper in the shadows suggests that {named_npc} has been trying to {move} {items} to another city, causing a turf war between the {faction} and the {other_faction}.", "The Imperial Military has been seen gathering in {district} after a report of a powerful ghost with ties to {named_npc} haunting the area.", "A shadowy {look} {warrior} named {generic_name} has been spotted near a {buildingType} in {district}, and a rumor says they're looking to acquire a {compound_item}.", "A well-known {profession} named {generic_name} is suspected of having a connection to a recent series of {moves} in {district}.", "An old legend about a powerful {compound_item} has resurfaced in {district}, causing both the {faction} and the {other_faction} to become interested.", "Whispers suggest that {named_npc} is offering a bounty for a mysterious {buildingItem} said to be hidden in a {buildingType}.", "The city watch is looking for a {look} {warrior} named {generic_name} who was last seen carrying a large sum of money from the {district}.", "Rumor has it that {named_npc} is hiding something in a {buildingType} in {district}, and a strange {buildingDetail} is the only clue."],
            factionRumors: ["The {faction} have put a bounty on {named_npc}, thought to be hiding in a {buildingType} in {district}.", "A well-known {profession} named {generic_name} is trying to make a deal with the {faction} to move some contraband.", "{faction} are said to be moving against the {other_faction}.", "An informant claims that {named_npc} has betrayed the {faction}, providing sensitive information to a rival organization.", "A raid by the Bluecoats on the {faction}'s lair in {district} has unearthed strange {items}.", "The {faction} are said to be secretly funding the construction of a new {buildingType} in {district}, which has been described as a {buildingDetail}.", "A secret message from the {faction} was intercepted by the {other_faction} in {district} detailing a plan to {move} valuable {items}.", "The {faction} are looking to recruit a new {profession} named {generic_name} to handle their affairs at a {buildingType} in {district}.", "A raid by the Bluecoats on the {faction}'s lair has revealed a dark secret about {named_npc} and their connection to a {compound_item}.", "An anonymous tip claims that the {faction} are secretly meeting at the {viceLocation} to discuss an upcoming war with the {other_faction}.", "Tension is high in {district} as the {faction} and the {other_faction} vie for control of a powerful {compound_item}."],
            characterRumors: ["{named_npc} has a secret that could bring down the {faction}.", "They say {named_npc}, the {warrior}, has been hiding a secret artifact in a {buildingType} in {district}.", "A well-known {profession} named {generic_name} wants to expose {named_npc} for their role in a recent heist in {district}.", "A {warrior} from {district} challenges {named_npc} to a duel near a {buildingType}.", "The mysterious {named_npc} has been seen in a {buildingType} in {district} trying to convince a member of the {faction} to {move} some rare {items}.", "An infamous {look} {profession} named {generic_name} with a goal of {goal} is rumored to be plotting against {named_npc} from a secret lair in a {buildingType} in {district}.", "A notorious {look} {warrior} named {generic_name} has been hired by the {faction} to challenge {named_npc} to a duel.", "The mysterious {named_npc} has been seen in {district} with a strange {compound_item}, and rumors suggest it's cursed.", "A well-known {profession} named {generic_name} is planning to expose {named_npc}'s past with the {faction} for their own personal gain.", "They say {named_npc} has been using their influence to protect a corrupt {profession} named {generic_name} who is hiding in a {buildingType}.", "The city watch is offering a reward for information on {named_npc}, who is wanted for a series of thefts of {items} from the {district}."],
            districtRumors: ["The city watch is cracking down on illicit activity in {district} after a series of disappearances.", "A ghost has been sighted haunting the streets of {district}, leading to a spike in spirit warden patrols.", "A new black market has been established in the back alleys of {district}, dealing in strange {items}.", "An anonymous flyer has appeared all over {district}, warning of a new cult with ties to {faction}.", "Citizens of {district} are demanding answers after a local {profession} named {generic_name} went missing with a large sum of money.", "Strange glowing sigils have been appearing on the buildings of {district}, causing superstitious folk to stay indoors.", "Whispers from the docks suggest that a strange cargo has arrived in {district}, causing a stir among the local {profession} named {generic_name}.", "A {buildingType} in {district} is a front for a smuggling operation run by a {look} {warrior} named {generic_name}.", "An unusually large number of ghosts have been seen near a {buildingType} in {district}, and the Spirit Wardens are investigating the disappearance of a {profession} named {generic_name}.", "The {faction} are trying to seize control of a {buildingType} in {district}, which is rumored to contain a powerful {compound_item}.", "A series of mysterious murders have occurred in {district}, and the only clue is a strange {buildingDetail} found near the bodies.", "The gambling at a {buildingType} in {district} has become so popular that it has attracted the attention of the {faction} and the {other_faction}."],
            itemRumors: ["A rare {items} has been seen in the possession of {named_npc} in a {buildingType} in {district}.", "The {faction} are offering a reward for information leading to the recovery of a missing {items}.", "Rumors say that a powerful {compound_item} is the key to unlocking a hidden vault in the depths of a {buildingType} in {district}.", "An auction for a strange and powerful {compound_item} is scheduled to take place in a secret location in a {buildingType} in {district}.", "The Spirit Wardens are investigating a series of bizarre incidents linked to a cursed {compound_item} from the {buildingType}.", "A recent heist in {district} was pulled off to steal a powerful {compound_item} from the {buildingType}.", "A {compound_item} has been stolen from a {buildingType} in {district}, and a bounty has been placed on the thief by {named_npc}.", "Rumors say that a valuable {buildingItem} has been found hidden behind a {buildingDetail} in a {buildingType} in {district}.", "A {compound_item} is said to grant the user a powerful ability, and both the {faction} and the {other_faction} are in a race to find it.", "A series of heists of similar {items} in {district} has been linked to a single individual, a {look} {warrior} named {generic_name}.", "A rare {buildingItem} is set to be auctioned off at a {buildingType} in {district}, drawing the attention of many powerful figures, including {named_npc}."],
            buildingRumors: ["The {buildingType} in {district} is said to be haunted by a ghost who guards a valuable {buildingItem}.", "Rumors whisper that a notorious criminal named {generic_name} is using a {buildingType} in {district} as a secret hideout.", "A strange message, written in blood, has appeared on the walls of a {buildingType} in {district}, covered in {buildingDetail}.", "The {faction} are rumored to be holding a secret meeting in the {buildingType} of {district} to discuss the theft of a {buildingItem}.", "A local {profession} named {generic_name} has been found murdered in the {buildingType} in {district}, a {buildingDetail} the only clue.", "It's said that {named_npc} is hiding a dark secret in the {buildingType} in {district}, somewhere behind the {buildingDetail}.", "The {buildingType} in {district} is being used as a secret meeting place for a new cult with ties to {named_npc}.", "A ghost has been sighted haunting the {buildingType} in {district}, and it's rumored to be the spirit of a murdered {profession} named {generic_name}."],
            viceRumors: ["They say {named_npc} is deeply in debt to {vicePurveyor} at the {viceLocation}, and is getting desperate.", "The {vicePurveyor} at {viceLocation} is rumored to have a hidden {compound_item} in their establishment.", "Word on the street is that a notorious crime boss, {named_npc}, is planning to move in on the gambling operation at the {viceLocation}.", "The gambling at {viceLocation} is fixed, and the {faction} are said to be behind it.", "The gambling at the {viceLocation} is rumored to be fixed by a {look} {warrior} named {generic_name} who has a goal of {goal}.", "A mysterious stranger has been seen at the {viceLocation} offering strange {items} in exchange for favors.", "They say a notorious criminal named {generic_name} is using the {viceLocation} as a place to recruit new members for their gang, {faction}."],
            catalystRumors: []
        };
        
        // Deep Cuts content
        const deepCutsData = {
            factions: ["The Church of The Weeping Lady", "The Reconciled", "The Brigade", "Cabbies", "Cyphers", "Dockers", "Gondoliers", "Sailors", "Laborers", "Servants", "Ink Rakes", "Ironworks Labor", "Rail Jacks", "Sparkrunners", "Tangletown", "The Unchained", "The Unity Commission", "Void Divers", "Skovlan Consulate", "Severosi Consulate", "Dagger Isles Consulate", "Iruvian Consulate", "Inspectors", "Rowan House", "Strangford House", "The Wraiths"],
            namedNpcs: ["Chief Pickett", "Captain Marsh", "Ellis Lockwood", "Hazel Chord", "Admiral Bowmore", "Commander Farros", "Sergeant Major Kain", "Jerett Keel", "Maraya Halili", "Carver Roth", "Chief Helker", "The Voice", "Grand Admiral Dunvil", "General Rowan", "Fleet Admiral Bowmore", "Captain Silas Dunvil", "Colonel Evelina Rowan", "Major Strangford", "Firash Dimabasa", "Tessa Brand", "Chief Inspector Coleburn", "Inspector Klev", "Warden Dunvil", "Master Morriston", "Trelloch", "Belle Brogan", "Hopper", "Lord Penderyn", "Lynthia Rowan", "Mylera Klev", "Arvus Clelland", "Lady Slane", "Brannon Gale", "Vondru Kaan", "Calica Malaahas", "Tuhan Sangara", "Arlen Skarvale", "Sergeant Kulcannan", "Fiana Blackvale", "Arbella Saltford", "Anson", "Five Twelve", "Pike", "Mason", "Master Chief Booker", "Lord Strangford", "Sera", "Arith", "Hester Vale", "Mara Keel", "Commander Bowmore", "Rolan Wott", "Jira", "Leclure", "Mordis", "Lochlan", "Tris", "Master Slane", "Corben", "Briggs", "Hutton"],
            districts: ["Skalla", "Memorial Park", "The Crows' Nest", "Cinder Downs"],
            professions: ["baker", "barber", "blacksmith", "brewer", "butcher", "carpenter", "cartwright", "chandler", "clerk", "cobbler", "cooper", "cultivator", "driver", "dyer", "embroiderer", "fishmonger", "gondolier", "guard", "leatherworker", "mason", "merchant", "roofer", "ropemaker", "rug maker", "servant", "tanner", "tinker", "vendor", "weaver", "woodworker", "goat herd", "messenger", "sailor", "advocate", "architect", "artist", "author", "bailiff", "apiarist", "banker", "jeweler", "locksmith", "scholar", "composer", "rail jack", "captain", "scribe", "steward", "soldier"],
            itemAdjectives: ["arcane", "clockwork", "blood-stained", "wailing", "hissing", "chitinous", "spectral", "oiled", "shadowy", "polished", "ornate", "cracked", "jagged", "shimmering", "ghastly", "scrimshawed", "bone-white", "ebon", "veridian", "tainted", "festering", "glimmering", "humid", "sputtering", "whirring"],
            itemNouns: ["dagger", "hammer", "locket", "tome", "key", "crystal", "lantern", "pistol", "watch", "ring", "skull", "lockpick"],
            items: ["haunted locket", "forbidden tome", "ghost key", "strange crystal", "tarnished compass", "carved glove", "arcane map", "whispering pipe"],
            tech_adjectives: ["steam-powered", "electro-charged", "void-infused", "clockwork", "hex-etched", "arcane", "galvanic", "dynamo", "spirit-fueled"],
            tech_nouns: ["combat gauntlet", "sniper rifle", "omni-tool", "rebreather", "grappling hook", "taser", "buzz-saw", "aetheric resonator", "plasmatic emitter", "chronometer", "spark-grenade"],
            deepcuts_items: ["Arclighter", "Glimmer", "Percussion Slug Gun", "Plasmographic Camera", "Bolton Autocycle", "Lightning Thrower", "Monitor Sphere", "Sparkrunner Rig"],
            vehicles: ["an Iruvian horse", "a penny-farthing bike", "a heavy Bolton Autocycle", "a modified Arclighter", "a whispering Lightning Thrower", "a haunted Sparkrunner Rig", "an experimental Monitor Sphere", "a lightning-charged Sparkrunner Rig", "a void-powered carriage"],
            buildingTypes: {
                "Skalla": ["inn", "tavern", "gambling hall", "apartment building", "small house", "shrine", "restaurant", "bar"],
                "Memorial Park": ["inn", "tavern", "gambling hall", "apartment building", "small house", "shrine", "restaurant", "bar"],
                "The Crows' Nest": ["inn", "tavern", "gambling hall", "apartment building", "small house", "shrine", "restaurant", "bar"],
                "Cinder Downs": ["inn", "tavern", "gambling hall", "apartment building", "small house", "shrine", "restaurant", "bar"]
            },
            vicePurveyors: {"Master Vreen": "Vreen's Hound Races", "Lady Freyla": "The Emperor's Cask"},
            viceLocations: {"Vreen's Hound Races": "Nightmarket", "Dundridge & Sons": "Nightmarket", "Spiregarden Theater": "Whitecrown"},
            generalRumors: ["An Imperial military unit has been seen testing a new weapon, a {compound_item}, near the {buildingType} in {district}.", "A rare technological innovation, a {tech_item}, has been stolen from a {buildingType} in {district}."],
            factionRumors: ["The {faction} are attempting to steal a new {tech_item} from the {other_faction} to gain a technological advantage.", "A high-ranking member of the {faction}, {named_npc}, has been seen publicly condemning the use of the new {tech_item}."],
            characterRumors: ["A {look} {warrior} named {generic_name} is wanted for the theft of a {tech_item} from the {district}.", "The mysterious {named_npc} has been seen driving a {vehicle} through {district} at breakneck speeds, and rumors suggest it's cursed."],
            districtRumors: ["A series of strange electrical surges near the {buildingType} in {district} have been linked to a new experimental device, a {tech_item}.", "The rooftops of {district} are becoming a new hotspot for smugglers using a {tech_item} to bypass patrols."],
            itemRumors: ["A strange and powerful {tech_item} has been found in a {buildingType} in {district}.", "An auction for a rare {tech_item} is scheduled to take place in a secret location in a {buildingType} in {district}.", "The Spirit Wardens are investigating a series of bizarre incidents linked to a cursed {compound_item} from the {buildingType}.", "A recent heist in {district} was pulled off to steal a powerful {compound_item} from the {buildingType}.", "A {compound_item} has been stolen from a {buildingType} in {district}, and a bounty has been placed on the thief by {named_npc}.", "Rumors say that a valuable {buildingItem} has been found hidden behind a {buildingDetail} in a {buildingType} in {district}.", "A {compound_item} is said to grant the user a powerful ability, and both the {faction} and the {other_faction} are in a race to find it.", "A series of heists of similar {items} in {district} has been linked to a single individual, a {look} {warrior} named {generic_name}.", "A rare {buildingItem} is set to be auctioned off at a {buildingType} in {district}, drawing the attention of many powerful figures, including {named_npc}."],
            buildingRumors: ["A {buildingType} in {district} is being used as a secret workshop for a new experimental device, a {tech_item}.", "A mysterious {named_npc} is hiding something in the {buildingType} in {district}, and a strange {tech_item} is the only clue."],
            viceRumors: ["They say {named_npc} is deeply in debt to {vicePurveyor} at the {viceLocation}, and is getting desperate.", "The {vicePurveyor} at {viceLocation} is rumored to have a hidden {compound_item} in their establishment.", "Word on the street is that a notorious crime boss, {named_npc}, is planning to move in on the gambling operation at the {viceLocation}.", "The gambling at {viceLocation} is fixed, and the {faction} are said to be behind it.", "The gambling at the {viceLocation} is rumored to be fixed by a {look} {warrior} named {generic_name} who has a goal of {goal}.", "A mysterious stranger has been seen at the {viceLocation} offering strange {items} in exchange for favors.", "They say a notorious criminal named {generic_name} is using the {viceLocation} as a place to recruit new members for their gang, {faction}."],
            catalystRumors: [
                "A fracture has opened in the sky above {district}, and a strange {compound_item} has fallen from it.",
                "A mysterious stranger named {generic_name} has appeared in {district}, and rumors suggest they are from another world.",
                "The city watch is offering a reward for information on a group of strangers who are believed to be plotting against the Imperial military.",
                "The Spirit Wardens are investigating a series of bizarre incidents linked to a cursed {compound_item} from the other side of the fracture}.",
                "An infamous {look} {warrior} named {generic_name} with a goal of {goal} is rumored to be plotting against {named_npc} from a secret lair in the {buildingType} in {district}.",
                "A whisper says a group of sparkrunners have discovered how to weaponize raw electroplasm with a newly forged {tech_item}.",
                "The city watch is baffled by a series of phantom thefts, believing a ghost is using a malfunctioning {tech_item} to bypass physical locks.",
                "A strange new cult has formed around a powerful {tech_item} discovered deep in the sewers of {district}."
            ]
        };
        
        // Factions without a leading "The" for correct grammar
        const factionsNoArticle = new Set([
            "Ulf Ironborn", "Foundation", "Council", "Spirit Wardens", "Imperial Military", "Sparkwrights", 
            "Skovlander Refugees", "Ecstasy of the Flesh", "The Church of The Weeping Lady", "The Reconciled", 
            "The Brigade", "Cabbies", "Cyphers", "Dockers", "Gondoliers", "Sailors", "Laborers", "Servants", 
            "Ink Rakes", "Ironworks Labor", "Rail Jacks", "Sparkrunners", "Tangletown", "The Unchained", 
            "The Unity Commission", "Void Divers", "Skovlan Consulate", "Severosi Consulate", 
            "Dagger Isles Consulate", "Iruvian Consulate", "Inspectors", "Rowan House", 
            "Strangford House", "The Wraiths", "The Unseen", "The Circle of Flame", "The Lampblacks", "The Dimmer Sisters",
            "The Billhooks", "The Gray Cloaks", "The Fog Hounds", "The Lost", "The Crows", "The Red Sashes",
            "The Silver Nails", "The Grinders", "The Forgotten Gods"
        ]);
        
        // The combined data source for generation
        let combinedData = {};
        
        // --- DOM Elements and Event Listeners ---
        const rumorDisplay = document.getElementById('rumorDisplay');
        const deepCutsToggle = document.getElementById('deepCutsToggle');
        const catalystBtn = document.getElementById('generateCatalyst');
        const catalystCheckbox = document.getElementById('includeCatalyst');
        const catalystCheckboxContainer = document.getElementById('catalystCheckboxContainer');
        const generateBatchBtn = document.getElementById('generateBatch');
        const generateHundredBtn = document.getElementById('generateHundred');
        const batchSizeInput = document.getElementById('batchSize');
        const clearAllBtn = document.getElementById('clearAllBtn');
        const copyAllBtn = document.getElementById('copyAllBtn'); // New button element
        const clearConfirmModal = document.getElementById('clearConfirmModal');
        const confirmClearBtn = document.getElementById('confirmClear');
        const cancelClearBtn = document.getElementById('cancelClear');
        const buttons = document.querySelectorAll('button');

        // Helper function to get a random item from an array
        const getRandomItem = arr => (arr && arr.length > 0) ? arr[Math.floor(Math.random() * arr.length)] : null;
        const capitalizeFirstLetter = string => string.charAt(0).toUpperCase() + string.slice(1);
        const generateGenericName = (dataSource) => {
            const prefix = getRandomItem(dataSource.namePrefixes);
            const suffix = getRandomItem(dataSource.nameSuffixes);
            return capitalizeFirstLetter(prefix) + suffix;
        };
        const generateCompoundItem = (dataSource) => {
            const adjective = getRandomItem(dataSource.itemAdjectives);
            const noun = getRandomItem(dataSource.itemNouns);
            return `${adjective} ${noun}`;
        };
        
        // New function to generate a technological item procedurally
        const generateTechItem = (dataSource) => {
            const adjective = getRandomItem(dataSource.tech_adjectives);
            const noun = getRandomItem(dataSource.tech_nouns);
            return `${adjective} ${noun}`;
        };

        // --- Rumor Generation Logic ---
        function generateRumorText(template, dataSource) {
            if (!template) return null;

            const generatedValues = {};
            
            // Generate all random values from the provided dataSource
            generatedValues.randomFaction = getRandomItem(dataSource.factions);
            generatedValues.randomOtherFaction = getRandomItem(dataSource.factions.filter(f => f !== generatedValues.randomFaction));
            generatedValues.randomNamedNpc = getRandomItem(dataSource.namedNpcs);
            generatedValues.randomGenericName = generateGenericName(dataSource);
            generatedValues.randomProfession = getRandomItem(dataSource.professions);
            generatedValues.randomWarrior = getRandomItem(dataSource.warriors);
            generatedValues.randomDistrict = getRandomItem(dataSource.districts);
            generatedValues.randomMove = getRandomItem(dataSource.moves);
            generatedValues.randomLook = getRandomItem(dataSource.looks);
            generatedValues.randomGoal = getRandomItem(dataSource.goals);
            
            // Item logic
            generatedValues.randomItems = getRandomItem(dataSource.items);
            generatedValues.randomCompoundItem = generateCompoundItem(dataSource);
            generatedValues.randomTechItem = generateTechItem(dataSource);
            generatedValues.randomDeepcutsItem = getRandomItem(dataSource.deepcuts_items);

            const possibleBuildingTypes = dataSource.buildingTypes[generatedValues.randomDistrict] || dataSource.buildingTypes["general"];
            generatedValues.randomBuildingType = getRandomItem(possibleBuildingTypes);
            generatedValues.randomBuildingDetail = getRandomItem(dataSource.buildingDetails);
            generatedValues.randomBuildingItem = getRandomItem(dataSource.buildingItems);

            const vicePurveyor = getRandomItem(Object.keys(dataSource.vicePurveyors));
            const viceLocation = dataSource.vicePurveyors[vicePurveyor];
            generatedValues.randomVicePurveyor = vicePurveyor;
            generatedValues.randomViceLocation = viceLocation;

            generatedValues.randomVehicle = getRandomItem(dataSource.vehicles);
            
            let rumor = template;

            const getFactionName = (faction) => {
                // Check if the faction is in our set of non-article factions
                if (factionsNoArticle.has(faction)) {
                    return faction;
                }
                return `The ${faction}`;
            };


            // Replace all placeholders with generated values
            rumor = rumor
                .replace(/{faction}/g, getFactionName(generatedValues.randomFaction))
                .replace(/{other_faction}/g, getFactionName(generatedValues.randomOtherFaction))
                .replace(/{named_npc}/g, generatedValues.randomNamedNpc)
                .replace(/{generic_name}/g, generatedValues.randomGenericName)
                .replace(/{profession}/g, generatedValues.randomProfession)
                .replace(/{warrior}/g, generatedValues.randomWarrior)
                .replace(/{district}/g, generatedValues.randomDistrict)
                .replace(/{move}/g, generatedValues.randomMove)
                .replace(/{items}/g, generatedValues.randomItems)
                .replace(/{compound_item}/g, generatedValues.randomCompoundItem)
                .replace(/{look}/g, generatedValues.randomLook)
                .replace(/{goal}/g, generatedValues.randomGoal)
                .replace(/{buildingType}/g, generatedValues.randomBuildingType)
                .replace(/{buildingDetail}/g, generatedValues.randomBuildingDetail)
                .replace(/{buildingItem}/g, generatedValues.randomBuildingItem)
                .replace(/{vicePurveyor}/g, generatedValues.randomVicePurveyor)
                .replace(/{viceLocation}/g, generatedValues.randomViceLocation)
                .replace(/{deepcuts_item}/g, generatedValues.randomDeepcutsItem)
                .replace(/{vehicle}/g, generatedValues.randomVehicle)
                .replace(/{tech_item}/g, generatedValues.randomTechItem);

            // Cleanup for better grammar
            const startsWithVowel = word => ['a', 'e', 'i', 'o', 'u'].includes(word.charAt(0).toLowerCase());

            // A/An logic
            rumor = rumor.replace(/a {look}/g, startsWithVowel(generatedValues.randomLook) ? `an ${generatedValues.randomLook}` : `a ${generatedValues.randomLook}`);
            rumor = rumor.replace(/a {profession}/g, startsWithVowel(generatedValues.randomProfession) ? `an ${generatedValues.randomProfession}` : `a ${generatedValues.randomProfession}`);
            rumor = rumor.replace(/a {warrior}/g, startsWithVowel(generatedValues.randomWarrior) ? `an ${generatedValues.randomWarrior}` : `a ${generatedValues.randomWarrior}`);
            rumor = rumor.replace(/a {buildingType}/g, startsWithVowel(generatedValues.randomBuildingType) ? `an ${generatedValues.randomBuildingType}` : `a ${generatedValues.randomBuildingType}`);

            rumor = rumor.replace(/A {look}/g, startsWithVowel(generatedValues.randomLook) ? `An ${generatedValues.randomLook}` : `A ${generatedValues.randomLook}`);
            rumor = rumor.replace(/A {profession}/g, startsWithVowel(generatedValues.randomProfession) ? `An ${generatedValues.randomProfession}` : `A ${generatedValues.randomProfession}`);
            rumor = rumor.replace(/A {warrior}/g, startsWithVowel(generatedValues.randomWarrior) ? `An ${generatedValues.randomWarrior}` : `A ${generatedValues.randomWarrior}`);
            rumor = rumor.replace(/A {buildingType}/g, startsWithVowel(generatedValues.randomBuildingType) ? `An ${generatedValues.randomBuildingType}` : `A ${generatedValues.randomBuildingType}`);

            rumor = rumor.replace(/long-haired/g, 'long-haired');
            rumor = rumor.replace(/glasses-wearing/g, 'glasses-wearing');

            // Final cleanup of extra spaces
            rumor = rumor.replace(/ +/g, ' ');

            // Capitalize the first letter of the whole string
            return rumor.charAt(0).toUpperCase() + rumor.slice(1);
        }

        function buildDataSource() {
            let baseData = { ...coreData };
            
            if (deepCutsToggle.checked) {
                // Combine core and deep cuts data
                baseData = {
                    factions: [...coreData.factions, ...deepCutsData.factions],
                    namedNpcs: [...coreData.namedNpcs, ...deepCutsData.namedNpcs],
                    namePrefixes: coreData.namePrefixes, // No deep cuts prefixes
                    nameSuffixes: coreData.nameSuffixes, // No deep cuts suffixes
                    districts: [...coreData.districts, ...deepCutsData.districts],
                    moves: coreData.moves, // No deep cuts moves
                    professions: [...coreData.professions, ...deepCutsData.professions],
                    warriors: coreData.warriors, // No deep cuts warriors
                    itemAdjectives: [...coreData.itemAdjectives, ...deepCutsData.itemAdjectives],
                    itemNouns: [...coreData.itemNouns, ...deepCutsData.itemNouns],
                    items: [...coreData.items, ...deepCutsData.items],
                    vehicles: [...coreData.vehicles, ...deepCutsData.vehicles],
                    looks: coreData.looks, // No deep cuts looks
                    goals: coreData.goals, // No deep cuts goals
                    buildingTypes: { ...coreData.buildingTypes, ...deepCutsData.buildingTypes },
                    buildingDetails: coreData.buildingDetails, // No deep cuts details
                    buildingItems: coreData.buildingItems, // No deep cuts items
                    vicePurveyors: { ...coreData.vicePurveyors, ...deepCutsData.vicePurveyors },
                    viceLocations: { ...coreData.viceLocations, ...deepCutsData.viceLocations },
                    generalRumors: [...coreData.generalRumors, ...deepCutsData.generalRumors],
                    factionRumors: [...coreData.factionRumors, ...deepCutsData.factionRumors],
                    characterRumors: [...coreData.characterRumors, ...deepCutsData.characterRumors],
                    districtRumors: [...coreData.districtRumors, ...deepCutsData.districtRumors],
                    itemRumors: [...coreData.itemRumors, ...deepCutsData.itemRumors],
                    buildingRumors: [...coreData.buildingRumors, ...deepCutsData.buildingRumors],
                    viceRumors: [...coreData.viceRumors, ...deepCutsData.viceRumors],
                    catalystRumors: deepCutsData.catalystRumors,
                    tech_adjectives: deepCutsData.tech_adjectives,
                    tech_nouns: deepCutsData.tech_nouns,
                    deepcuts_items: deepCutsData.deepcuts_items
                };
            } else {
                // Fallback to core data lists for deepcuts-specific categories if toggle is off
                baseData.tech_adjectives = [];
                baseData.tech_nouns = [];
                baseData.deepcuts_items = [];
                baseData.catalystRumors = [];
            }

            return baseData;
        }

        // --- Firebase Functions ---
        async function saveRumor(rumorText) {
            if (!userId) {
                console.error("User not authenticated. Cannot save rumor.");
                return;
            }
            try {
                const rumorsCol = collection(db, `artifacts/${appId}/users/${userId}/rumors`);
                await addDoc(rumorsCol, {
                    text: rumorText,
                    timestamp: serverTimestamp()
                });
            } catch (e) {
                console.error("Error adding document: ", e);
            }
        }

        async function clearAllRumors() {
            if (!userId) {
                console.error("User not authenticated. Cannot clear rumors.");
                return;
            }
            setButtonsDisabled(true);
            const rumorsCol = collection(db, `artifacts/${appId}/users/${userId}/rumors`);
            const querySnapshot = await getDocs(rumorsCol);
            
            // Delete documents in a batch to be more efficient
            const deletePromises = querySnapshot.docs.map(doc => deleteDoc(doc.ref));
            await Promise.all(deletePromises);
            setButtonsDisabled(false);
        }

        function setupRumorListener() {
            if (!userId) {
                console.error("User not authenticated. Cannot set up listener.");
                return;
            }
            const rumorsCol = collection(db, `artifacts/${appId}/users/${userId}/rumors`);
            const q = query(rumorsCol);
            
            onSnapshot(q, (querySnapshot) => {
                const rumors = [];
                querySnapshot.forEach((doc) => {
                    rumors.push({
                        text: doc.data().text,
                        timestamp: doc.data().timestamp
                    });
                });

                // Sort the rumors in memory by timestamp
                rumors.sort((a, b) => (b.timestamp?.toDate() || 0) - (a.timestamp?.toDate() || 0));

                renderRumors(rumors.map(r => r.text));
            });
        }

        function renderRumors(rumors) {
            rumorDisplay.innerHTML = '';
            if (rumors.length === 0) {
                rumorDisplay.innerHTML = `<p class="p-6 bg-[#161c28] border border-[#2d3a4b] rounded-lg text-center text-lg md:text-xl italic">
                    Click a button below to generate a new rumor.
                </p>`;
            } else {
                rumors.forEach(rumorText => {
                    const rumorItem = document.createElement('p');
                    rumorItem.className = 'rumor-item';
                    rumorItem.textContent = rumorText;
                    rumorDisplay.appendChild(rumorItem);
                });
            }
        }

        function setButtonsDisabled(disabled) {
            buttons.forEach(button => {
                button.disabled = disabled;
                if (disabled) {
                    button.classList.add('opacity-50', 'cursor-not-allowed');
                } else {
                    button.classList.remove('opacity-50', 'cursor-not-allowed');
                }
            });
        }

        async function generateAndSaveSingleRumor(rumorType) {
            setButtonsDisabled(true);
            const dataSource = buildDataSource();
            let template;
            switch(rumorType) {
                case 'general': template = getRandomItem(dataSource.generalRumors); break;
                case 'faction': template = getRandomItem(dataSource.factionRumors); break;
                case 'character': template = getRandomItem(dataSource.characterRumors); break;
                case 'district': template = getRandomItem(dataSource.districtRumors); break;
                case 'item': template = getRandomItem(dataSource.itemRumors); break;
                case 'building': template = getRandomItem(dataSource.buildingRumors); break;
                case 'vice': template = getRandomItem(dataSource.viceRumors); break;
                case 'catalyst': template = getRandomItem(dataSource.catalystRumors); break;
                default: return;
            }
            const rumorText = generateRumorText(template, dataSource);
            if (rumorText) {
                await saveRumor(rumorText);
            }
            setButtonsDisabled(false);
        }

        async function generateAndSaveBatchRumors(batchSize) {
            const size = parseInt(batchSize, 10);
            if (isNaN(size) || size < 1 || size > 100) {
                rumorDisplay.innerHTML = `<p class="rumor-item !bg-red-800 !border-red-600">Please enter a number between 1 and 100.</p>`;
                return;
            }

            setButtonsDisabled(true);
            const dataSource = buildDataSource();
            const selectedTemplates = [];
            document.querySelectorAll('.rumor-type-checkbox').forEach(checkbox => {
                if (checkbox.checked) {
                    switch (checkbox.id) {
                        case 'includeGeneral': selectedTemplates.push(...dataSource.generalRumors); break;
                        case 'includeFaction': selectedTemplates.push(...dataSource.factionRumors); break;
                        case 'includeCharacter': selectedTemplates.push(...dataSource.characterRumors); break;
                        case 'includeDistrict': selectedTemplates.push(...dataSource.districtRumors); break;
                        case 'includeItem': selectedTemplates.push(...dataSource.itemRumors); break;
                        case 'includeBuilding': selectedTemplates.push(...dataSource.buildingRumors); break;
                        case 'includeVice': selectedTemplates.push(...dataSource.viceRumors); break;
                        case 'includeCatalyst': selectedTemplates.push(...dataSource.catalystRumors); break;
                    }
                }
            });

            if (selectedTemplates.length === 0) {
                rumorDisplay.innerHTML = `<p class="rumor-item !bg-red-800 !border-red-600">Please select at least one rumor type.</p>`;
                setButtonsDisabled(false);
                return;
            }

            for (let i = 0; i < size; i++) {
                const template = getRandomItem(selectedTemplates);
                const rumorText = generateRumorText(template, dataSource);
                if (rumorText) {
                    await saveRumor(rumorText);
                }
            }
            setButtonsDisabled(false);
        }

        // --- Event Listeners ---
        document.getElementById('generateGeneral').addEventListener('click', () => generateAndSaveSingleRumor('general'));
        document.getElementById('generateFaction').addEventListener('click', () => generateAndSaveSingleRumor('faction'));
        document.getElementById('generateCharacter').addEventListener('click', () => generateAndSaveSingleRumor('character'));
        document.getElementById('generateDistrict').addEventListener('click', () => generateAndSaveSingleRumor('district'));
        document.getElementById('generateItem').addEventListener('click', () => generateAndSaveSingleRumor('item'));
        document.getElementById('generateBuilding').addEventListener('click', () => generateAndSaveSingleRumor('building'));
        document.getElementById('generateVice').addEventListener('click', () => generateAndSaveSingleRumor('vice'));
        catalystBtn.addEventListener('click', () => generateAndSaveSingleRumor('catalyst'));
        generateBatchBtn.addEventListener('click', () => generateAndSaveBatchRumors(batchSizeInput.value));
        generateHundredBtn.addEventListener('click', () => generateAndSaveBatchRumors(100));

        // Show confirmation modal before clearing
        clearAllBtn.addEventListener('click', () => {
            clearConfirmModal.classList.remove('hidden');
        });

        // Handle confirmation
        confirmClearBtn.addEventListener('click', () => {
            clearConfirmModal.classList.add('hidden');
            clearAllRumors();
        });

        // Handle cancellation
        cancelClearBtn.addEventListener('click', () => {
            clearConfirmModal.classList.add('hidden');
        });

        // Handle copy to clipboard
        copyAllBtn.addEventListener('click', () => {
            const rumors = Array.from(document.querySelectorAll('.rumor-item'));
            const rumorText = rumors.map(p => p.textContent).join('\n\n');

            if (rumorText) {
                // Use a temporary textarea to handle the copy command
                const tempTextarea = document.createElement('textarea');
                tempTextarea.value = rumorText;
                document.body.appendChild(tempTextarea);
                tempTextarea.select();
                
                try {
                    document.execCommand('copy');
                    const originalText = copyAllBtn.textContent;
                    copyAllBtn.textContent = 'Copied!';
                    setTimeout(() => copyAllBtn.textContent = originalText, 2000);
                } catch (err) {
                    console.error('Failed to copy text: ', err);
                } finally {
                    document.body.removeChild(tempTextarea);
                }
            }
        });

        deepCutsToggle.addEventListener('change', () => {
            const isChecked = deepCutsToggle.checked;
            catalystBtn.classList.toggle('hidden', !isChecked);
            catalystCheckboxContainer.classList.toggle('hidden', !isChecked);
        });

        // Initialize state based on toggle
        deepCutsToggle.dispatchEvent(new Event('change'));

    </script>
</body>
</html>
