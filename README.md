


/****************************Beginning of Program*****************************/


#include <iostream>
#include <fstream>
#include <cstdlib>
#include <string>
#include <iomanip>

using namespace std;

//*****************************************************************************
//                                 Structures                                **
//*****************************************************************************
//Variables that will change while the character fights a monster
//These variables will be referenced in the combat function
struct Variable_char_stats
{
    long exp;
    int gold;
    short level;
    short point;            //All of these are self-explanatory
    short mp_potion;
    short hp_potion;
};

//Constants that must NOT change while the character fights in a dungeon but
//certain situations will allow these variables to change such as character death
//or character level up.
struct Constant_char_stats
{
    string name;      //Stores character name, which is also the file name
    short class_;     //Character class is stored as an integer. 1= knight 2 = slayer 3 = priest
    short hp_a;       //Stamine Attribute
    short mp_a;       //Wisdom Attribute
    short def_a;      //Endurance Attribute
    short atk_a;      //Strength Attribute
    int hp;           //HP Stat
    int mp;           //MP Stat
    double def;       //Defense Stat
    double atk;       //Attack Stat
    short type;       //1 = Regular 2 = Hardcore 0 = Dead, No longer played
};

//These are monster attributes.
//Each area will assign a specific value to these variables
struct Monster
{
    int hp;             //HP of regular monsters
    int def;            //Defense of regular monsters
    int atk;            //Damage of regular monsters
    int boss_hp;        //Boss's default HP
    int boss_def;       //Boss's default defense
    int boss_atk;       //Boss's default attack
    int exp;            //How much exp regular monsters give
    int gold;           //How much gold regular monsters drop
    int boss_exp;       //How much exp boss gives
    int boss_gold;      //How much gold boss drops
    int id;             //Defines the monster type. Regular or Boss
    int boss_sp_hp1;    //When a boss reaches this hp, it will use special ability.
    int boss_sp_hp2;    //When a boss reaches this hp, it will use another ability.
    int boss_sp_atk;    //Boss ability that enhances its attack
    int boss_sp_def;    //Boss ability that enhances its defense
};

//This structure stores monsters' name
struct Monster_name
{
    string name1;       //Name of monster 1
    string name2;       //Name of monster 2
    string name3;       //Name of monster 3
    string name4;       //Name of monster 4
    string name5;       //Name of the boss
};
//*****************************************************************************
//                           Function Prototypes                             **
//*****************************************************************************
//These are self-explanatory
void description();
void menu();

//This function lets user create a character
void charcreate();

//This function opens character file and import necessary data.
//Then it will send all the data to character information screen function
//This function has an example of Pointers
void loadchar();

//Guide functions - self explanatory
void guide();
void guide_char();
void guide_class();
void guide_attribute();
void guide_skills();
void guide_areas();
void guide_level();
void guide_combat();
void guide_potionshop();

//Receives character data. It is a main character screen.
//User can choose to shop, change attributes, or train
void char_info_screen(Constant_char_stats,Variable_char_stats);

//Functions for potion shop, allocating att. points, or resetting attributes
void shop(Constant_char_stats,Variable_char_stats);
void add_attribute(Constant_char_stats,Variable_char_stats);
void reset_attribute(Constant_char_stats,Variable_char_stats);

//If user chooses to train, he will be sent here.
//This function receives all the character data too.
void mission_screen(Constant_char_stats,Variable_char_stats);

//Functions for each training area.
//This function receives all the character data and also
//create monster data and send them all to combat preparation function
void area1(Constant_char_stats,Variable_char_stats);
void area2(Constant_char_stats,Variable_char_stats);
void area2(Constant_char_stats,Variable_char_stats);
void area3(Constant_char_stats,Variable_char_stats);
void area4(Constant_char_stats,Variable_char_stats);
void area5(Constant_char_stats,Variable_char_stats);
void area6(Constant_char_stats,Variable_char_stats);
void area7(Constant_char_stats,Variable_char_stats);
void area8(Constant_char_stats,Variable_char_stats);
void area9(Constant_char_stats,Variable_char_stats);
void area10(Constant_char_stats,Variable_char_stats);

//This function receives all the character and monster data from each area
//This function organizes character and monster data.
//It will send each monster data to the class-specific combat function
//After a monster is defeated or player escapes, this function will write
//any updated data to the character file, then send the next monster data to
//the combat function. Back and forth
void combat_prep(Constant_char_stats,Variable_char_stats,Monster,Monster_name);

//These are functions for combat with monsters. Class Specific.
//Note that Variable character stats are referenced. Those variables are changed during combat.
//Constant character stats are NOT allowed to change unless done by death or levelup functions.
void combat_knight(Constant_char_stats &,Variable_char_stats &,Monster,string);
void combat_slayer(Constant_char_stats &,Variable_char_stats &,Monster,string);
void combat_priest(Constant_char_stats &,Variable_char_stats &,Monster,string);

//After completing a dungeon, this function saves all the updated data to character file. It will receive
//the updated data from battle prep function
void battle_over(string);

//This function shows player's and monster's health bar
void show_bar(int,int,int,int,int,int);

//This function shows user how much experience he needs to level up.
void show_next_level(short);

//This function checks to see if player has leveled. It also defines how much
//experience player needs to level up
void level_up_screen(Constant_char_stats &,Variable_char_stats &);

//This function penalizes player for dying
//If the character is hardcore, this function will assign constant.type
//a value of 0 and the load function will not open the character file
void death_screen(Constant_char_stats,Variable_char_stats);

//*****************************************************************************
//                               Main Function                               **
//*****************************************************************************
//Not much going on here
int main()
{
    description();
    menu();
}

//*****************************************************************************
//                       Program Description Function                        **
//*****************************************************************************
//Description of Program
void description()
{
    cout
    << "\n\n\n\n\n\t\t\tProgram Description\n\n\n"
    << "This program is a Text-Based Role-Playing Game for a Single Player.\n"
    << "It has three classes: Knight, Slayer and Priest. They have their own\n"
    << "unique stats, abilities, and distinct playing style.\n"
    << "This is a short game with 10 Areas, 50 monsters, and 10 maximum\n"
    << "character levels. For more information, please read the game guides.\n"
    << "\nI hope you enjoy!.\n\n"
    << setw(70) << "Michael Park\n\n";
}

//*****************************************************************************
//                               Menu function.                              **
//*****************************************************************************
//Game Menu
void menu()
{
    cout << string(3, '\n');

    //Variable that stores user choice
    int choice;

    //Show menu to user
    cout
    << "\n\n\n\n\t********\tGAME MENU\t********\n"
    << "\t1. Create New Character\n"
    << "\t2. Start Game\n"
    << "\t3. Game Guide\n"
    << "\t4. Exit\n"
    << "\t****************************************\n\n"
    << "Please enter your choice: ";

    cin >> choice;

    //Input Validation for menu choice
    while(choice < 1 || choice > 4)
    {
        cout << "Please enter a valid choice: ";
        cin >> choice;
    }

    //Send user to the function of his/her choice
    if(choice == 1)
    {
        charcreate();
    }

    else if(choice == 2)
    {
        loadchar();
    }

    else if(choice == 3)
    {
        guide();
    }

    else
    {
        exit(0);
    }
}

//*****************************************************************************
//                               Guide Function                              **
//*****************************************************************************
void guide()
{
    cout << string(5, '\n');

    //Variable to store user choice
    int choice;

    cout
    << "\n\n\n\t\tGuide\n\n\n\n\n"
    << "\t1. Character\n\n"
    << "\t2. Classes\n\n"
    << "\t3. Level / Experience\n\n"
    << "\t4. Attributes / Character Stats\n\n"
    << "\t5. Skills\n\n"
    << "\t6. Areas / Dungeons\n\n"
    << "\t7. Combat\n\n"
    << "\t8. Potion / Potion Shop\n\n"
    << "\t9. Back to Menu\n\n\n"
    << "Please enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 9)
    {
        cout << "Please enter a valid choice: ";
        cin >> choice;
    }

    //Takes user to each guide depending on his choice
    if(choice == 1)
    {
        guide_char();
    }
    else if(choice == 2)
    {
        guide_class();
    }
    else if(choice == 3)
    {
        guide_level();
    }
    else if(choice == 4)
    {
        guide_attribute();
    }
    else if(choice == 5)
    {
        guide_skills();
    }
    else if(choice == 6)
    {
        guide_areas();
    }
    else if(choice == 7)
    {
        guide_combat();
    }
    else if(choice == 8)
    {
        guide_potionshop();
    }
    else
    {
        menu();
    }
}

//*****************************************************************************
//                           Character Guide Function                        **
//*****************************************************************************
void guide_char()
{
    //Stores user input
    int choice;

    cout
    << "\n\n\n\n\n\t\tCharacter\n\n\n\n"
    << "There are two types of characters in this game.\n\n"
    << "Regular characters are for beginners and they will be revived after death.\n\n"
    << "Hardcore characters are for people that already have played this game\n"
    << "and want a more challenging gameplay. Hardcore characters have only 1 life.\n"
    << "After a hardcore character dies, it can no longer be played.\n\n\n"
    << "To Create a Character\n\n"
    << "1. Choose a class\n"
    << "2. Choose Regular or Hardcore\n"
    << "3. Choose a name for your character\n";

    cout << "\n\n\nTo return to Game Guide, please enter 1.\n";
    cin >> choice;

    while(choice != 1)
    {
        cout << "Please enter 1 to go back to Game Guide.\n";

        cin >> choice;
    }

    guide();
}

//*****************************************************************************
//                             Class Guide Function                          **
//*****************************************************************************
void guide_class()
{
    //Stores user input
    int choice;

    cout
    << "\n\n\n\n\n\t\tClasses\n\n\n"
    << "There are 3 classes in this game: Knight, Slayer, Priest.\n\n"
    << "Knights have a high defense rating and low attack rating. They have the\n"
    << "best survivability, but they can't kill the enemies quicky\n\n"
    << "Slayers have a high attack rating and low defense rating. They can't take\n"
    << "a lot of hits, but they can kill the enemies quickly. This class is\n"
    << "recommended for players that want to run through the game quickly.\n\n"
    << "Priests are skill-dependent and require the most micromanagement out of all\n"
    << "three classes. Priests have the lowerst survivability, but they can enhance\n"
    << "their defense through skills.\n";

    cout << "\n\n\nTo return to Game Guide, please enter 1.\n";
    cin >> choice;

    while(choice != 1)
    {
        cout << "Please enter 1 to go back to Game Guide.\n";

        cin >> choice;
    }

    guide();
}

//*****************************************************************************
//                          Character Level Guide Function                   **
//*****************************************************************************
void guide_level()
{
    //Stores user input
    int choice;

    cout
    << "\n\n\n\n\n\t\tLevel / Experience\n\n\n"
    << "All new characters will start at Level 1 with 0 Experience.\n"
    << "Every time you kill a monster, you gain experience. Once you have enough\n"
    << "experience, you will level up.\n\n\n\n"
    << "Here is the required experience for each level.\n\n"
    << "Level 2: 5000\n\n"
    << "Level 3: 10000\n\n"
    << "Level 4: 50000\n\n"
    << "Level 5: 100000\n\n"
    << "Level 6: 500000\n\n"
    << "Level 7: 1000000\n\n"
    << "Level 8: 5000000\n\n"
    << "Level 9: 10000000\n\n"
    << "Level 10: 50000000\n\n\n"
    << "Level 10 is the maximum level in this game.\n\n\n"
    << "Benefits of Level-Up\n\n"
    << "Your character's stats will increase (Your character gets stronger).\n"
    << "You have a chance to try harder areas (instances) and gain more loots\n"
    << "and experiences.\n"
    << "You will gain attribute points to furthur increase any stats of your choice.\n"
    << "Effect of potions and skills will increase.\n\n";

    cout << "\n\n\nTo return to Game Guide, please enter 1.\n";
    cin >> choice;

    while(choice != 1)
    {
        cout << "Please enter 1 to go back to Game Guide.\n";

        cin >> choice;
    }

    guide();
}

//*****************************************************************************
//                      Character Attribute Guide Function                   **
//*****************************************************************************
void guide_attribute()
{
    //Stores user input
    int choice;

    cout
    << "\n\n\n\n\n\t\tAttributes and Character Stats\n\n\n"
    << "There are 4 character stats in this game.\n\n"
    << "HP (Hit Point): This is your character's health. Once your HP reaches 0, your\n"
    << "character will die. HP can be replenished with potions only.\n\n"
    << "MP (Magic Point): This is your character's mana. It is required to use\n"
    << "various skills. It can be replenished by using non-skill attack or potions.\n"
    << "Priests will naturally gain a certain amount of MP after each turn.\n\n"
    << "Defense: This determines your character's ability to resist/withstand enemies'\n"
    << "attacks. The higher the defense, the less damage your character takes.\n\n"
    << "Attack: This determines how much damage you deal to enemies.\n\n\n\n"
    << "For each Level-Up, Your character receives the following stats bonus.\n\n"
    << "Knight: + 2000 HP, + 20 MP, + 300 Defense, + 350 Attack\n\n"
    << "Slayer: + 2500 HP, +20 MP, + 250 Defense, + 500 Attack\n\n"
    << "Priest: + 100 HP, +1500 MP, + 250 Defense, + 400 Attack\n\n\n\n\n"
    << "Normally, you can not directly change your character's stats, but, you can\n"
    << "increase your stats through attributes. When you level up, you receive a\n"
    << "certain number of attribute points. These points can be spent on any attributes\n"
    << "of your choice to increase your character's stats.\n\n\n"
    << "Vitality: Determines your character's Hit Point (HP)\n"
    << "Wisdom: Determines your character's Magic Point (MP)\n"
    << "Endurance: Determines your character's Defense\n"
    << "Strength: Determines your character's Attack\n\n\n\n"
    << "One attribute point will provide following bonus\n\n\n"
    << "Vitality\n"
    << "Knight gains 500 HP.\n"
    << "Slayer gains 450 HP.\n"
    << "Priest gains 50 HP.\n\n\n"
    << "Wisdom\n"
    << "Knight gains 20 MP.\n"
    << "Slayer gains 20 MP.\n"
    << "Priest gains 400 MP.\n\n\n"
    << "Endurance\n"
    << "Knight gains 30 Defense.\n"
    << "Slayer gains 25 Defense.\n"
    << "Priest gains 20 Defense.\n\n\n"
    << "Strength\n"
    << "Knight gains 350 Attack.\n"
    << "Slayer gains 550 Attack.\n"
    << "Priest gains 410 Attack.\n\n\n\n"
    << "Attribute Points Rewarded for Each Level-Up\n\n"
    << "Level 2: 10\n"
    << "Level 3: 15\n"
    << "Level 4: 20\n"
    << "Level 5: 25\n"
    << "Level 6: 30\n"
    << "Level 7: 35\n"
    << "Level 8: 40\n"
    << "Level 9: 45\n"
    << "Level 10 100\n\n\n"
    << "TIP: It is recommended that every class invest the majority of the points in\n"
    << "strength because monster's HP will significantly increase.\n\n"
    << "TIP: Priests naturally have a very low HP and need to use Mana Barrier to use\n"
    << "their MP to absorb enemy attacks.";



    cout << "\n\n\nTo return to Game Guide, please enter 1.\n";
    cin >> choice;

    while(choice != 1)
    {
        cout << "Please enter 1 to go back to Game Guide.\n";

        cin >> choice;
    }

    guide();
}

//*****************************************************************************
//                       Character Skill Guide Function                      **
//*****************************************************************************
void guide_skills()
{
    //Stores user input
    int choice;

    cout
    << "\n\n\n\n\n\t\tSkills\n\n\n\n"
    << "All skills comsume character's Magic Point (MP).\n\n\n"
    << "There are 2 types of skills for every class\n\n\n"
    << "Buff Skill: Enhances one of the character's stats. Player will not lose\n"
    << "his turn after using buff skills.\n\n"
    << "Attack Skill: Multiplies character's attack and deals damage to enemies\n"
    << "After attack skills are executed, the player will lose his turn and the\n"
    << "monster will attack the player.\n\n\n\n"
    << "Knight\n\n"
    << "Iron Will: A buff skill that increases defense\n"
    << "Shield Bash: An attack skill that multiplies attack by a factor of 2\n"
    << "Charge: An attack skill that multiplies attack by a factor of 5\n\n\n"
    << "Slayer\n\n"
    << "Berserk: A buff skill that increases attack\n"
    << "Slash: An attack skill that multiplies attack by a factor of 3\n"
    << "Penetration: An attack skill that multiplies attack by a factor of 5\n\n\n"
    << "Priest\n\n"
    << "Salvation: A buff skill that allows priest's MP to absorb all damage\n"
    << "Redemption: A buff skill that increases defense\n"
    << "Meditation: A buff skill that increases MP gained after each turn\n"
    << "Heaven's Fist: An attack skill that multiplies attack by a factor of 2\n"
    << "Final Judgment: An attack skill that multiplies attack by a factor of 5\n";


    cout << "\n\n\nTo return to Game Guide, please enter 1.\n";
    cin >> choice;

    while(choice != 1)
    {
        cout << "Please enter 1 to go back to Game Guide.\n";

        cin >> choice;
    }

    guide();
}

//*****************************************************************************
//                               Area Guide Function                         **
//*****************************************************************************
void guide_areas()
{
    //Stores user input
    int choice;

    cout
    << "\n\n\n\n\n\t\tAreas/Dungeons\n\n\n\n"
    << "There are 10 areas in this game with 5 monsters in each.\n"
    << "It is always recommended that you train in an area that matches your\n"
    << "character level. For example, the first area (Training Ground 1) is\n"
    << "designed for Level 1 character. The first 4 monsters are minions or\n"
    << "regular monsters with normal stats. The fifth monster is a boss and\n"
    << "has higher stats than the minions.\n\n\n\n"
    << "All bosses have the following special abilities:\n\n\n"
    << "1. At 50% HP, attack rating increase.\n\n"
    << "2. At 25% HP, another attack rating increase and defense rating increase.\n"
    << "\n\nYou will see the warning when the boss's HP reaches the specified\n"
    << "percentages. At 50%, bosses will be enraged. At 25%, the bosses will be\n"
    << "infuriated.\n\n"
    << "The bosses are harder to kill than normal monsters, but they drop more\n"
    << "gold and provide better experience.";


    cout << "\n\n\nTo return to Game Guide, please enter 1.\n";
    cin >> choice;

    while(choice != 1)
    {
        cout << "Please enter 1 to go back to Game Guide.\n";

        cin >> choice;
    }

    guide();
}

//*****************************************************************************
//                           Combat Guide Function                           **
//*****************************************************************************
void guide_combat()
{
    //Stores user input
    int choice;

    cout
    << "\n\n\n\n\n\t\tCombat\n\n\n\n"
    << "The combat in this game is turn-based. The player will have a chance to\n"
    << "make the first move on a monster. Once the monster takes damage, it will\n"
    << "then attack the player back. This rotation will continue until either one\n"
    << "of them dies.\n\n"
    << "If the player levels up during a fight, the player will be notified and will\n"
    << "have a chance to get out of the area and use attribute points.\n\n"
    << "If the player dies during a fight, regular characters will be revived right away\n"
    << "with 0 gold. Hardcore characters will not be playable.";



    cout << "\n\n\nTo return to Game Guide, please enter 1.\n";
    cin >> choice;

    while(choice != 1)
    {
        cout << "Please enter 1 to go back to Game Guide.\n";

        cin >> choice;
    }

    guide();
}

//*****************************************************************************
//                           Potion Shop Guide Function                      **
//*****************************************************************************
void guide_potionshop()
{
    //Stores user input
    int choice;

    cout
    << "\n\n\n\n\n\t\tPotions/Potion Shop\n\n\n\n"
    << "Potions are necessary in this game, especially during boss fights.\n"
    << "Players do not lose their turn for drinking potions. In fact, they\n"
    << "can drink more than 1 potion in the same turn if needed.\n\n"
    << "HP Potions will return a certain amount of player's HP.\n"
    << "MP Potions will return a certain amount of player's MP.\n\n"
    << "How much MP or HP a potion replenishes depends on the player's level.\n"
    << "Players can visit Potion Shop to purchase potions for 200 gold each.";



    cout << "\n\n\nTo return to menu, please enter 1.\n";
    cin >> choice;

    while(choice != 1)
    {
        cout << "Please enter 1 to go back to Game Guide.\n";

        cin >> choice;
    }

    guide();
}

//*****************************************************************************
//                         Character Creation Function                       **
//*****************************************************************************
void charcreate()
{
    cout << string(5, '\n');

    //Save user class as a integer value
    short class_;

    //Save the type of character. Regular or Hardcore
    short type;


    //Display classes
    cout
    << "\n\n\n\t******\tClass Selection\t******\n"
    << "\t1. Knight\n"
    << "\t2. Slayer\n"
    << "\t3. Priest\n"
    << "\t******************************\n\n"
    << "Please select a class: ";

    //Save user input
    cin >> class_;

    //while loop for input validation
    while(class_ < 1 || class_ > 3)
    {
        cout << "\n\nPlease enter a valid choice: ";
        cin >> class_;
    }

    cout
    << "\n\n\t1. Regular Character (Can be Resurrected)\n"
    << "\t2. Hardcore Character (One Life)\n\n"
    << "Please enter your choice: ";

    cin >> type;

    while(type < 1 || type > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> type;
    }

    //Save character name
    string charname;

    //Prompt user to enter a name
    cout << "\nPlease enter the name of your character: ";

    cin >> charname;

//*****************************************************************************
//           This is a test to see if the file already exists.               **
//       If the file can be opened, it means the name already exists.        **
//           Therefore, the user cannot use that name.                       **
//*****************************************************************************
    ifstream testfile;

    testfile.open(charname.c_str());

    while(testfile)
    {
        testfile.close();

        cout
        << "\n\nA character with that name already exists.\n"
        << "Please choose a different name: ";

        cin >> charname;

        testfile.open(charname.c_str());
    }
//*****************************************************************************
//*****************************************************************************
    //ouput file stream
    ofstream charinfo;

    //Create and open a file with the name that user entered
    charinfo.open(charname.c_str());

    //If file is created, write character data to the file.
    //Use if statements for each class since each class has
    //different initial data. These are all initial stats for
    //each class.
    if(charinfo)
    {
        if(class_ == 1)
        {
            charinfo
            << charname << endl
            << class_ << endl
            << '1' << endl
            << '1' << endl
            << '1' << endl
            << '1' << endl
            << "500" << endl
            << "100" << endl
            << '1' << endl
            << '0' << endl
            << '0' << endl
            << '0' << endl
            << "35" << endl
            << "100" << endl
            << "10" << endl
            << "10" << endl
            << type;
        }
        else if(class_ == 2)
        {
            charinfo
            << charname << endl
            << class_ << endl
            << '1' << endl
            << '1' << endl
            << '1' << endl
            << '1' << endl
            << "300" << endl
            << "100" << endl
            << '1' << endl
            << '0' << endl
            << '0' << endl
            << '0' << endl
            << "30" << endl
            << "290" << endl
            << "10" << endl
            << "10" << endl
            << type;
        }
        else
        {
            charinfo
            << charname << endl
            << class_ << endl
            << '1' << endl
            << '1' << endl
            << '1' << endl
            << '1' << endl
            << "150" << endl
            << "1250" << endl
            << '1' << endl
            << '0' << endl
            << '0' << endl
            << '0' << endl
            << "25" << endl
            << "150" << endl
            << "10" << endl
            << "10" << endl
            << type;
        }

        //Close the file when finished
        charinfo.close();

        //Let user know that character creation is successful
        cout
        << "\n\nCharacter created successfully.\n\n"
        << "You can start playing this character now.\n";
    }
    //If file cannot be created, prompt user to try again
    //Send user back to the beginning of character create function
    else
    {
        cout
        << "\nCharacter Creation Failed.\n"
        << "Please try again with a different name.\n\n";

        charcreate();
    }

    //Send user to menu when finished.
    menu();
}

//*****************************************************************************
//                           Load Character Function                         **
//*****************************************************************************
void loadchar()
{
    cout << string(10, '\n');

    //Create input file stream
    ifstream char_;

    //Variable to store user input, character name
    string charname;

    cout << "\n\nPlease enter the name of your character: ";
    cin >> charname;

    //All these variables will be stored in a struct
    string name;
    short class_;
    short hp_a;
    short mp_a;
    short def_a;
    short atk_a;
    int hp;
    int mp;
    short level;
    long exp;
    int gold;
    short point;
    double def;
    double atk;
    short mp_potion;
    short hp_potion;
    short type;

    //Using Pointers to reference the address of variables def and atk
    //-----------------------------------------------------------------
    double *ptr_def;
    double *ptr_atk;

    ptr_def = &def;
    ptr_atk = &atk;
    //-----------------------------------------------------------------
    char_.open(charname.c_str());

    if(char_)
    {
        //Read each data element from the character file
        char_ >> charname >> class_ >> hp_a >> mp_a >> def_a >> atk_a >> hp
        >> mp >> level >> exp >> gold >> point >> def >> atk >> mp_potion
        >> hp_potion >> type;

        //Close file when finished
        char_.close();

        //Prevent user from opening a dead hardcore character
        //This is what they chose after all.....
        if(type == 0)
        {
            cout
            << "\n\nThis hardcore character has died in the previous game.\n"
            << "Your Deeds of Valor Will be Remembered.\n"
            << "R.I.P.\n\n";

            //Send user back to menu to try again
            menu();
        }
    }
    //If char_ can not be opened, it means the character does not exist
    else
    {
        cout
        << "\nThat character does not exist.\n"
        << "If you have not, please create a character first.";

        menu();
    }

    //Create structured data to send to another function
    Constant_char_stats constant;
    Variable_char_stats variable;

    constant.name = charname;
    constant.class_ = class_;
    constant.hp_a = hp_a;
    constant.mp_a = mp_a;
    constant.def_a = def_a;
    constant.atk_a = atk_a;
    constant.hp = hp;
    constant.mp = mp;
    //------Assigning the Contents of the Pointer to Struct Variables--------
    constant.def = *ptr_def;
    constant.atk = *ptr_atk;
    //-----------------------------------------------------------------------

    variable.exp = exp;
    variable.gold = gold;
    variable.level = level;
    variable.point = point;
    variable.mp_potion = mp_potion;
    variable.hp_potion = hp_potion;
    constant.type = type;

    //After all the data are organized, send them all to character information
    //screen and let user start playing the game
    char_info_screen(constant,variable);
}

//*****************************************************************************
//                   Character Information Screen Function                   **
//*****************************************************************************

void char_info_screen(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(5, '\n');

    //Variable to store user choice
    int choice;

    //string variable to store class
    string class_screen;

    //String variable to store character type
    string type_screen;

    //Convert the class integer to a string
    if(constant.class_ == 1)
    {
        class_screen = "Knight";
    }
    else if(constant.class_ == 2)
    {
        class_screen = "Slayer";
    }
    else
    {
        class_screen = "Priest";
    }

    if(constant.type == 1)
    {
        type_screen = "Regular";
    }

    if(constant.type == 2)
    {
        type_screen = "Hardcore";
    }

    //Main Screen
    cout
    << "\n\n\n\n\n\n\n\n"
    << constant.name << '\n' << setw(70) << type_screen << "\n\n"
    << "\n\n\nLevel " << variable.level << '\t' << class_screen << "\t\t"
    << "Experience:\t" << variable.exp << "\n\n\n\n"
    << "*********Attribute*********\n"
    << "Stamina:\t\t" << constant.hp_a << '\n'
    << "Wisdom:\t\t\t" << constant.mp_a << '\n'
    << "Endurance:\t\t" << constant.def_a << '\n'
    << "Strength:\t\t" << constant.atk_a << '\n'
    << "***************************\n\n\n"
    << "HP:\t\t" << constant.hp << '\n'
    << "MP:\t\t" << constant.mp << '\n'
    << "Attack:\t\t" << constant.atk << '\n'
    << "Defense:\t" << constant.def << "\n\n\n\n\n"
    << "Items:\t" << variable.hp_potion << " HP Potions\t\t" << variable.mp_potion
    << " MP Potions" << "\n\nGold: " << variable.gold
    << "\n\n\n\n\n\t\t\tOptions\n\n\n"
    << "\t1. Train\n\n"
    << "\t2. Potion Shop\n\n"
    << "\t3. Use Attribute Points (You have " << variable.point << " points)\n\n"
    << "\t4. Reset All Attributes (Cost: 15000 Gold)\n\n"
    << "\t5. Back to Menu\n\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    //Input Validation
    while(choice < 1 || choice > 5)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    //Conditional statements for the menu
    if(choice == 1)
    {
        mission_screen(constant,variable);
    }
    else if(choice == 2)
    {
        shop(constant,variable);
    }
    else if(choice == 3)
    {
        add_attribute(constant,variable);
    }
    else if(choice == 4)
    {
        reset_attribute(constant,variable);
    }
    else
    {
        menu();
    }
}

//*****************************************************************************
//                           Potion Shop Function                            **
//*****************************************************************************
void shop(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(5, '\n');

    //Prevent user from entering the shop if he does not have enough gold
    if(variable.gold < 200)
    {
        cout << "\n\n\n\nYou do not have any gold to buy anything here\n\n";

        char_info_screen(constant,variable);
    }

    //Variable to store user choice
    int choice;

    //Potion Shop Menu
    cout << "\n\n\n\n\t\tWelcome to the Potion Shop\n\n\n";

    cout
    << "\nYou have " << variable.gold << " gold.\n\n"
    << "\t1. Buy HP potions (200 Gold each)\n"
    << "\t2. Buy MP potions (200 Gold Each)\n"
    << "\t3. Back to Character Screen\n"
    << "\nPlease enter your choice: ";

    cin >> choice;

    //There's always somebody that does not enter a number between 1 and 3
    while(choice < 1 || choice > 3)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        //Stores the number of potions user wants to buy
        int quantity;

        //Stores total price
        int total;

        cout << "How many HP potions would you like to buy: ";
        cin >> quantity;

        //Calculate total price
        total = 200 * quantity;

        //If user does not have enough gold..
        while(total > variable.gold)
        {
            cout
            << "You do not have enough gold.\n"
            << "Please choose a different quantity: ";
            cin >> quantity;

            total = 200 * quantity;
        }

        //Subtract the total price from user's gold
        variable.gold -= (quantity * 200);

        cout << "\n\nTransaction Complete";

        //Give user the potions that he purchased
        variable.hp_potion += quantity;

        cout
        << "\n\nYou have obtained " << quantity << " HP Potions."
        << "\nYou have a total of " << variable.hp_potion << " HP Potions."
        << "\nYou have " << variable.gold << " gold left.\n\n";
    }
    else if(choice == 2)
    {
        //Stores the number of potions user wants to buy
        int quantity;

        //Stores total price
        int total;

        cout << "How many MP potions would you like to buy: ";
        cin >> quantity;

        total = 200 * quantity;

        while(total > variable.gold)
        {
            cout
            << "You do not have enough gold.\n"
            << "Please choose a different quantity: ";
            cin >> quantity;

            total = 200 * quantity;
        }

        variable.gold -= (quantity * 200);

        cout << "\n\nTransaction Complete";

        variable.mp_potion += quantity;

        cout
        << "\n\nYou have obtained " << quantity << " MP Potions."
        << "\nYou have a total of " << variable.mp_potion << " MP Potions."
        << "\nYou have " << variable.gold << " gold left.\n\n";
    }
    //User wants to go back to character information screen
    else
    {
        char_info_screen(constant,variable);
    }

    //Save the updated data to the character file
    ofstream update;

    update.open(constant.name.c_str());

    update
    << constant.name << endl
    << constant.class_ << endl
    << constant.hp_a << endl
    << constant.mp_a << endl
    << constant.def_a << endl
    << constant.atk_a << endl
    << constant.hp << endl
    << constant.mp << endl
    << variable.level << endl
    << variable.exp << endl
    << variable.gold << endl
    << variable.point << endl
    << constant.def << endl
    << constant.atk << endl
    << variable.mp_potion << endl
    << variable.hp_potion << endl
    << constant.type;

    update.close();

    //Send user back to the character information screen
    char_info_screen(constant,variable);
}

//*****************************************************************************
//                       Allocate Attribute Points Function                  **
//*****************************************************************************
void add_attribute(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    if(variable.point == 0)
    {
        cout << "\n\n\nYou do not have any attribute points at this moment.";

        char_info_screen(constant,variable);
    }

    //Variable to store user choice
    int choice;

    //do-while loop for menu
    do
    {
        cout
        << "\n\n\n\n\n\nYou have " << variable.point << " attribute points to use."
        << "\n\n\n\n\tStamina will increase your max Hit Points. (HP)\n\n"
        << "\tWisdom will increase your max Magic Points. (MP)\n\n"
        << "\tEndurance will increase your Defense. \n\n"
        << "\tStrength will increase your Attack.\n\n\n\n"
        << "\t1. Add Points in Stamina\n\n"
        << "\t2. Add Points in Wisdom\n\n"
        << "\t3. Add Points in Endurance\n\n"
        << "\t4. Add Points in Strength\n\n"
        << "\t5. Back to Character Screen\n\n\n"
        << "Please enter your choice: ";

        cin >> choice;

        //Again, input validation
        while(choice < 1 || choice > 5)
        {
            cout << "\nPlease enter a valid choice: ";
            cin >> choice;
        }

        if(choice == 1)
        {
            //Variable to store the number of points user wants in an attribute
            int points;

            cout << "\nHow many points would you like to add in Stamina: ";
            cin >> points;

            while(points > variable.point)
            {
                cout
                << "\nYou only have " << variable.point << " points.\n"
                << "Please enter a correct number: ";

                cin >> points;
            }

            //Subtract user entered number from his total attribute points
            //Then add user entered number to Stamina
            variable.point -= points;
            constant.hp_a += points;

            cout
            << "\nYou have added " << points << " points in Stamina.\n\n"
            << "Your Stamina is now " << constant.hp_a;

            //Each class gets a different value from each attribute point
            if(constant.class_ == 1)
            {
                constant.hp += (points * 500);
            }

            else if(constant.class_ == 2)
            {
                constant.hp += (points * 450);
            }
            else
            {
                constant.hp += (points * 50);
            }

            cout << "\n\nYour HP is now " << constant.hp;
        }
        else if(choice == 2)
        {
            //Variable to store the number of points user wants in an attribute
            int points;

            cout << "\nHow many points would you like to add in Wisdom: ";
            cin >> points;

            while(points > variable.point)
            {
                cout
                << "\nYou only have " << variable.point << " points.\n"
                << "Please enter a correct number: ";

                cin >> points;
            }

            variable.point -= points;
            constant.mp_a += points;

            cout
            << "\nYou have added " << points << " points in Wisdom.\n\n"
            << "Your Wisdom is now " << constant.mp_a;

            if(constant.class_ == 1)
            {
                constant.mp += (points * 20);
            }

            else if(constant.class_ == 2)
            {
                constant.mp += (points * 20);
            }
            else
            {
                constant.mp += (points * 400);
            }

            cout << "\n\nYour MP is now " << constant.mp;
        }
        else if(choice == 3)
        {
            //Variable to store the number of points user wants in an attribute
            int points;

            cout << "\nHow many points would you like to add in Endurance: ";
            cin >> points;

            while(points > variable.point)
            {
                cout
                << "\nYou only have " << variable.point << " points.\n"
                << "Please enter a correct number: ";

                cin >> points;
            }

            variable.point -= points;
            constant.def_a += points;

            cout
            << "\nYou have added " << points << " points in Endurance.\n\n"
            << "Your Endurance is now " << constant.def_a;

            if(constant.class_ == 1)
            {
                constant.def += (points * 30);
            }

            else if(constant.class_ == 2)
            {
                constant.def += (points * 25);
            }
            else
            {
                constant.def += (points * 20);
            }

            cout << "\n\nYour Defense is now " << constant.def;
        }
        else if(choice == 4)
        {
            //Variable to store the number of points user wants in an attribute
            int points;

            cout << "\nHow many points would you like to add in Strength: ";
            cin >> points;

            while(points > variable.point)
            {
                cout
                << "\nYou only have " << variable.point << " points.\n"
                << "Please enter a correct number: ";

                cin >> points;
            }

            variable.point -= points;
            constant.atk_a += points;

            cout
            << "\nYou have added " << points << " points in Strength.\n\n"
            << "Your Strength is now " << constant.atk_a;

            if(constant.class_ == 1)
            {
                constant.atk += (points * 370);
            }

            else if(constant.class_ == 2)
            {
                constant.atk += (points * 550);
            }
            else
            {
                constant.atk += (points * 410);
            }

            cout << "\n\nYour Attack is now " << constant.atk;
        }
        else
        {
            char_info_screen(constant,variable);
        }

        //Save the new data to the character file
        ofstream update;

        update.open(constant.name.c_str());

        update
        << constant.name << endl
        << constant.class_ << endl
        << constant.hp_a << endl
        << constant.mp_a << endl
        << constant.def_a << endl
        << constant.atk_a << endl
        << constant.hp << endl
        << constant.mp << endl
        << variable.level << endl
        << variable.exp << endl
        << variable.gold << endl
        << variable.point << endl
        << constant.def << endl
        << constant.atk << endl
        << variable.mp_potion << endl
        << variable.hp_potion << endl
        << constant.type;

        update.close();
    }while((choice == 1 || choice == 2 || choice == 3 || choice == 4) && variable.point > 0);

    char_info_screen(constant,variable);
}

//*****************************************************************************
//                           Reset Attributes Function                       **
//*****************************************************************************
void reset_attribute(Constant_char_stats constant,Variable_char_stats variable)
{
    int choice; //Stores user choice

    //If user has 1 for all attributes, he does not need to reset any points.
    if(constant.hp_a == 1 && constant.mp_a == 1 && constant.def_a == 1
    && constant.atk_a == 1)
    {
        cout << "\nYou can not reset any attributes.\n";
        char_info_screen(constant,variable);
    }

    //If user does not have 4000 gold, he is sent back to character information
    if(variable.gold < 15000)
    {
        cout << "\nYou do not have enough gold to pay for this service.\n";
        char_info_screen(constant,variable);
    }

    cout
    << "\n\n\n\t\tAre you sure you want to reset all of your attributes?\n\n\n"
    << "\t1. Yes\n\n"
    << "\t2. No\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";

        cin >> choice;
    }

    if(choice == 1)
    {

    }
    else
    {
        char_info_screen(constant,variable);
    }

    //This variable stores the points that user will obtain after resetting
    int point = (constant.hp_a - 1) + (constant.mp_a - 1) + (constant.def_a - 1)
    + (constant.atk_a - 1);

    //User gets the reset points
    variable.point += point;

    //All attributes are reset to 1
    constant.hp_a = 1;
    constant.mp_a = 1;
    constant.def_a = 1;
    constant.atk_a = 1;

    //User pays 4000 gold for this service
    variable.gold -= 15000;

    //Converts all attributes to starting values + level-up bonus stats
    if(constant.class_ == 1)
    {
        constant.hp = 500 + ((variable.level - 1) * 2000);
        constant.mp = 100 + ((variable.level - 1) * 20);
        constant.def = 35 + ((variable.level - 1) * 300);
        constant.atk = 100 + ((variable.level - 1) * 350);
    }
    else if(constant.class_ == 2)
    {
        constant.hp = 300 + ((variable.level - 1) * 2500);
        constant.mp = 100 + ((variable.level - 1) * 20);
        constant.def = 30 + ((variable.level - 1) * 250);
        constant.atk = 290 + ((variable.level - 1) * 500);
    }
    else
    {
        constant.hp = 150 + ((variable.level - 1) * 100);
        constant.mp = 1250 + ((variable.level - 1) * 1500);
        constant.def = 25 + ((variable.level - 1) * 250);
        constant.atk = 150 + ((variable.level - 1) * 400);
    }



    cout
    << "\nYou have successfully reset all of your attributes\n"
    << "\nYou have " << variable.point << " points to use\n";

    //Save the new data to the character file
    ofstream update;

    update.open(constant.name.c_str());

    update
    << constant.name << endl
    << constant.class_ << endl
    << constant.hp_a << endl
    << constant.mp_a << endl
    << constant.def_a << endl
    << constant.atk_a << endl
    << constant.hp << endl
    << constant.mp << endl
    << variable.level << endl
    << variable.exp << endl
    << variable.gold << endl
    << variable.point << endl
    << constant.def << endl
    << constant.atk << endl
    << variable.mp_potion << endl
    << variable.hp_potion << endl
    << constant.type;

    update.close();

    //User is sent back to character information screen
    char_info_screen(constant,variable);
}

//*****************************************************************************
//                           Mission Screen Function                         **
//*****************************************************************************
void mission_screen(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Variable to store user choice
    int choice;

    cout
    << "\n\n\n\t\tPlease Choose from the Following Missions\n\n\n\n\n"
    << "\t1. Training Ground \t\t(Lv. 1)\n\n"
    << "\t2. Training Ground \t\t(Lv. 2)\n\n"
    << "\t3. Training Ground \t\t(Lv. 3)\n\n"
    << "\t4. Darkquaver Woods \t\t(Lv. 4)\n\n"
    << "\t5. Wyrmgorge \t\t\t(Lv. 5)\n\n"
    << "\t6. Colossal Ruins \t\t(Lv. 6)\n\n"
    << "\t7. Eldritch Academy \t\t(Lv. 7)\n\n"
    << "\t8. Sienna Canyon \t\t(Lv. 8)\n\n"
    << "\t9. Dark Cathedral \t\t(Lv. 9)\n\n"
    << "\t10. Labyrinth of Terror \t(Lv. 10)\n\n"
    << "\t11. Back to Character Screen\n\n"
    << "Please enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 11)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        area1(constant,variable);
    }
    else if(choice == 2)
    {
        area2(constant,variable);
    }
    else if(choice == 3)
    {
        area3(constant,variable);
    }
    else if(choice == 4)
    {
        area4(constant,variable);
    }
    else if(choice == 5)
    {
        area5(constant,variable);
    }
    else if(choice == 6)
    {
        area6(constant,variable);
    }
    else if(choice == 7)
    {
        area7(constant,variable);
    }
    else if(choice == 8)
    {
        area8(constant,variable);
    }
    else if(choice == 9)
    {
        area9(constant,variable);
    }
    else if(choice == 10)
    {
        area10(constant,variable);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 1 Function                             **
//*****************************************************************************
//This function contains data on monsters in the first area
void area1(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 1500;
    monster.def = 20;
    monster.atk = 90;
    monster.boss_hp = 5300;
    monster.boss_def = 40;
    monster.boss_atk = 100;
    monster.exp = 927;
    monster.gold = 842;
    monster.boss_exp = 1473;
    monster.boss_gold = 2351;
    monster.id = 0;
    monster.boss_sp_hp1 = 2650;
    monster.boss_sp_hp2 = 1325;
    monster.boss_sp_atk = 20;
    monster.boss_sp_def = 50;

    mon_name.name1 = "Mutated Hog";
    mon_name.name2 = "Rabid Coyote";
    mon_name.name3 = "Forrest Bear";
    mon_name.name4 = "Forrest Tiger";
    mon_name.name5 = "Nine-Tailed Fox (BOSS)";

    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout << "\n\n\t\tWelcome to Training Ground 1\n\n\n\n"
    << "Here, you will have the opportunity to hone your skills. You will\n"
    << "also gain experience and loots. Although this area is designated as \n"
    << "a training site, you can still die if you are not careful.\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 2 Function                             **
//*****************************************************************************
void area2(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 45000;
    monster.def = 350;
    monster.atk = 650;
    monster.boss_hp = 220000;
    monster.boss_def = 500;
    monster.boss_atk = 800;
    monster.exp = 983;
    monster.gold = 1256;
    monster.boss_exp = 1582;
    monster.boss_gold = 3564;
    monster.id = 0;
    monster.boss_sp_hp1 = 110000;
    monster.boss_sp_hp2 = 55000;
    monster.boss_sp_atk = 100;
    monster.boss_sp_def = 200;

    mon_name.name1 = "Aurmane Yak";
    mon_name.name2 = "Azure Gorilla";
    mon_name.name3 = "Arzakaar Guardian";
    mon_name.name4 = "Ghilliedhu";
    mon_name.name5 = "Teralith (BOSS)";

    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout << "\n\n\t\tWelcome to Training Ground 2\n\n\n\n"
    << "Here, you will have the opportunity to hone your skills. You will\n"
    << "also gain experience and loots. Although this area is designated as \n"
    << "a training site, you can still die if you are not careful.\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 3 Function                             **
//*****************************************************************************
void area3(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 84000;
    monster.def = 1500;
    monster.atk = 1500;
    monster.boss_hp = 325000;
    monster.boss_def = 3000;
    monster.boss_atk = 2000;
    monster.exp = 6650;
    monster.gold = 2256;
    monster.boss_exp = 15321;
    monster.boss_gold = 6326;
    monster.id = 0;
    monster.boss_sp_hp1 = 162500;
    monster.boss_sp_hp2 = 81250;
    monster.boss_sp_atk = 500;
    monster.boss_sp_def = 500;

    mon_name.name1 = "Brass Steward";
    mon_name.name2 = "Zuulhound";
    mon_name.name3 = "Broll";
    mon_name.name4 = "Brass Conservator";
    mon_name.name5 = "Baruldur (BOSS)";

    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout << "\n\n\t\tWelcome to Training Ground 3\n\n\n\n"
    << "Here, you will have the opportunity to hone your skills. You will\n"
    << "also gain experience and loots. Although this area is designated as \n"
    << "a training site, you can still die if you are not careful.\n"
    << "This is the last training mission before you will face the real challange.\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 4 Function                             **
//*****************************************************************************
void area4(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 120000;
    monster.def = 5000;
    monster.atk = 1800;
    monster.boss_hp = 480000;
    monster.boss_def = 6500;
    monster.boss_atk = 2500;
    monster.exp = 8167;
    monster.gold = 3141;
    monster.boss_exp = 16245;
    monster.boss_gold = 7115;
    monster.id = 0;
    monster.boss_sp_hp1 = 240000;
    monster.boss_sp_hp2 = 120000;
    monster.boss_sp_atk = 550;
    monster.boss_sp_def = 1000;

    mon_name.name1 = "Chamaas Scout";
    mon_name.name2 = "Chamas Sentinel";
    mon_name.name3 = "Chamas Stabber";
    mon_name.name4 = "Chamas Invader";
    mon_name.name5 = "Gilgash (BOSS)";
    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout
    << "\n\n\t\tWelcome to Darkquaver Woods\n\n\n\n"
    << "Mission: Defeat Gilgash and his minions\n\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 5 Function                             **
//*****************************************************************************
void area5(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 200000;
    monster.def = 10000;
    monster.atk = 2300;
    monster.boss_hp = 1000000;
    monster.boss_def = 15000;
    monster.boss_atk = 3000;
    monster.exp = 59342;
    monster.gold = 4350;
    monster.boss_exp = 161240;
    monster.boss_gold = 8260;
    monster.id = 0;
    monster.boss_sp_hp1 = 500000;
    monster.boss_sp_hp2 = 250000;
    monster.boss_sp_atk = 1000;
    monster.boss_sp_def = 5000;

    mon_name.name1 = "Ebon Imp";
    mon_name.name2 = "Maceras Orisk";
    mon_name.name3 = "Blade Golem";
    mon_name.name4 = "Mangled Ruffian";
    mon_name.name5 = "Maskimxuul (BOSS)";

    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout
    << "\n\n\t\tWelcome to Wyrmgorge\n\n\n\n"
    << "Mission: Defeat Maskimxuul and his minions\n\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 6 Function                             **
//*****************************************************************************
void area6(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 300000;
    monster.def = 15000;
    monster.atk = 3000;
    monster.boss_hp = 1500000;
    monster.boss_def = 20000;
    monster.boss_atk = 4500;
    monster.exp = 82452;
    monster.gold = 4726;
    monster.boss_exp = 170192;
    monster.boss_gold = 8700;
    monster.id = 0;
    monster.boss_sp_hp1 = 750000;
    monster.boss_sp_hp2 = 375000;
    monster.boss_sp_atk = 1000;
    monster.boss_sp_def = 7000;

    mon_name.name1 = "Fang Grunt";
    mon_name.name2 = "Fang Orcan";
    mon_name.name3 = "Fang Rager";
    mon_name.name4 = "Fang Ravager";
    mon_name.name5 = "Tyrak (BOSS)";

    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout
    << "\n\n\t\tWelcome to Colossal Ruins\n\n\n\n"
    << "Mission: Defeat Tyrak and his minions\n\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 7 Function                             **
//*****************************************************************************
void area7(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 400000;
    monster.def = 20000;
    monster.atk = 3700;
    monster.boss_hp = 2000000;
    monster.boss_def = 30000;
    monster.boss_atk = 5000;
    monster.exp = 658324;
    monster.gold = 5260;
    monster.boss_exp = 1366704;
    monster.boss_gold = 9427;
    monster.id = 0;
    monster.boss_sp_hp1 = 1000000;
    monster.boss_sp_hp2 = 500000;
    monster.boss_sp_atk = 1500;
    monster.boss_sp_def = 10000;

    mon_name.name1 = "Bisque Doll";
    mon_name.name2 = "Bisque Lady";
    mon_name.name3 = "Bisque Marionette";
    mon_name.name4 = "Chaos Mistress";
    mon_name.name5 = "Eldritch Eye (BOSS)";
    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout
    << "\n\n\t\tWelcome to Eldritch Academy\n\n\n\n"
    << "Mission: Defeat Eldritch Eye and his minions\n\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 8 Function                             **
//*****************************************************************************
void area8(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 500000;
    monster.def = 25000;
    monster.atk = 4700;
    monster.boss_hp = 2500000;
    monster.boss_def = 40000;
    monster.boss_atk = 5800;
    monster.exp = 714285;
    monster.gold = 5742;
    monster.boss_exp = 1826132;
    monster.boss_gold = 9920;
    monster.id = 0;
    monster.boss_sp_hp1 = 1250000;
    monster.boss_sp_hp2 = 625000;
    monster.boss_sp_atk = 2000;
    monster.boss_sp_def = 15000;

    mon_name.name1 = "Vengeful Defender";
    mon_name.name2 = "Vengeful Outrider";
    mon_name.name3 = "Vengeful Raider";
    mon_name.name4 = "Vengeful Warmaster";
    mon_name.name5 = "Tarnos (BOSS)";
    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout
    << "\n\n\t\tWelcome to Sienna Canyon\n\n\n\n"
    << "Mission: Defeat Tarnos and his minions\n\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 9 Function                             **
//*****************************************************************************
void area9(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 600000;
    monster.def = 30000;
    monster.atk = 5200;
    monster.boss_hp = 3000000;
    monster.boss_def = 50000;
    monster.boss_atk = 6500;
    monster.exp = 4132689;
    monster.gold = 6239;
    monster.boss_exp = 11326541;
    monster.boss_gold = 10232;
    monster.id = 0;
    monster.boss_sp_hp1 = 1500000;
    monster.boss_sp_hp2 = 750000;
    monster.boss_sp_atk = 2500;
    monster.boss_sp_def = 20000;

    mon_name.name1 = "Annihilation Charger";
    mon_name.name2 = "Annihilation Destroyer";
    mon_name.name3 = "Annihilation Reaper";
    mon_name.name4 = "Annihilation Vanquisher";
    mon_name.name5 = "Vyjalek (BOSS)";
    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout
    << "\n\n\t\tWelcome to Dark Cathedral\n\n\n\n"
    << "Mission: Defeat Vyjalek and his minions\n\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Area 10 Function                            **
//*****************************************************************************
void area10(Constant_char_stats constant,Variable_char_stats variable)
{
    cout << string(10, '\n');

    //Monster Data Structure
    Monster monster;
    Monster_name mon_name;

    monster.hp = 1000000;
    monster.def = 35000;
    monster.atk = 6300;
    monster.boss_hp = 5000000;
    monster.boss_def = 60000;
    monster.boss_atk = 7500;
    monster.exp = 0;
    monster.gold = 6856;
    monster.boss_exp = 0;
    monster.boss_gold = 11258;
    monster.id = 0;
    monster.boss_sp_hp1 = 2500000;
    monster.boss_sp_hp2 = 1250000;
    monster.boss_sp_atk = 3000;
    monster.boss_sp_def = 25000;

    mon_name.name1 = "Nightmare Kornus";
    mon_name.name2 = "Nightmare Kelsaik";
    mon_name.name3 = "Nightmare Kakun";
    mon_name.name4 = "Nightmare Krivalga";
    mon_name.name5 = "Iskaragard (BOSS)";
    //Save user choice
    int choice;

    //Welcome the user to area 1
    cout
    << "\n\n\t\tWelcome to Labyrinth of Terror\n\n\n\n"
    << "Mission: Defeat Iskaragard and his minions\n\n"
    << "Good Luck and Have Fun!\n\n\n\n\n"
    << "\t1. Start Training\n\n"
    << "\t2. Leave\n\n\n"
    << "Enter your choice: ";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";
        cin >> choice;
    }

    if(choice == 1)
    {
        combat_prep(constant,variable,monster,mon_name);
    }
    else
    {
        char_info_screen(constant,variable);
    }
}

//******************************************************************************
//                       Combat Preparation Function                          **
//******************************************************************************
//This is where character and monster data are organized and updated
//before user enters each combat
void combat_prep(Constant_char_stats constant,Variable_char_stats variable,
Monster monster,Monster_name mon_name)
{
    //Store user choice
    int choice;

    /*****************************First Monster********************************/
    if(constant.class_ == 1)
    {
        combat_knight(constant,variable,monster,mon_name.name1);
    }
    else if(constant.class_ == 2)
    {
        combat_slayer(constant,variable,monster,mon_name.name1);
    }
    else
    {
        combat_priest(constant,variable,monster,mon_name.name1);
    }

    //Save the new data to the character file
    ofstream update;

    update.open(constant.name.c_str());

    update
    << constant.name << endl
    << constant.class_ << endl
    << constant.hp_a << endl
    << constant.mp_a << endl
    << constant.def_a << endl
    << constant.atk_a << endl
    << constant.hp << endl
    << constant.mp << endl
    << variable.level << endl
    << variable.exp << endl
    << variable.gold << endl
    << variable.point << endl
    << constant.def << endl
    << constant.atk << endl
    << variable.mp_potion << endl
    << variable.hp_potion << endl
    << constant.type;

    update.close();
    /*************************************************************************/


    /*****************************Second Monster********************************/
    //Ask User if he wants to continue to fight monsters
    cout
    << "\n\n\n\n"
    << "Would you like to fight the next monster?\n\n"
    << "Enter 1 to Continue. Enter 2 to Quit.\n";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";

        cin >> choice;
    }

    if(choice == 1)
    {

    }
    else
    {
        battle_over(constant.name);
    }

    //Send user to the Combat Function according to class
    if(constant.class_ == 1)
    {
        combat_knight(constant,variable,monster,mon_name.name2);
    }
    else if(constant.class_ == 2)
    {
        combat_slayer(constant,variable,monster,mon_name.name2);
    }
    else
    {
        combat_priest(constant,variable,monster,mon_name.name2);
    }

    //Save the new data to the character file
    update.open(constant.name.c_str());

    update
    << constant.name << endl
    << constant.class_ << endl
    << constant.hp_a << endl
    << constant.mp_a << endl
    << constant.def_a << endl
    << constant.atk_a << endl
    << constant.hp << endl
    << constant.mp << endl
    << variable.level << endl
    << variable.exp << endl
    << variable.gold << endl
    << variable.point << endl
    << constant.def << endl
    << constant.atk << endl
    << variable.mp_potion << endl
    << variable.hp_potion << endl
    << constant.type;

    update.close();
    /*************************************************************************/


    /*****************************Third Monster********************************/
    //Ask User if he wants to continue to fight monsters
    cout
    << "\n\n\n\n"
    << "Would you like to fight the next monster?\n\n"
    << "Enter 1 to Continue. Enter 2 to Quit.\n";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";

        cin >> choice;
    }

    if(choice == 1)
    {

    }
    else
    {
        battle_over(constant.name);
    }

    //Send user to battle
    if(constant.class_ == 1)
    {
        combat_knight(constant,variable,monster,mon_name.name3);
    }
    else if(constant.class_ == 2)
    {
        combat_slayer(constant,variable,monster,mon_name.name3);
    }
    else
    {
        combat_priest(constant,variable,monster,mon_name.name3);
    }

    //Save the new data to the character file
    update.open(constant.name.c_str());

    update
    << constant.name << endl
    << constant.class_ << endl
    << constant.hp_a << endl
    << constant.mp_a << endl
    << constant.def_a << endl
    << constant.atk_a << endl
    << constant.hp << endl
    << constant.mp << endl
    << variable.level << endl
    << variable.exp << endl
    << variable.gold << endl
    << variable.point << endl
    << constant.def << endl
    << constant.atk << endl
    << variable.mp_potion << endl
    << variable.hp_potion << endl
    << constant.type;

    update.close();
    /***************************************************************************/


    /*****************************Fourth Monster********************************/
    //Ask User if he wants to continue to fight monsters
    cout
    << "\n\n\n\n"
    << "Would you like to fight the next monster?\n\n"
    << "Enter 1 to Continue. Enter 2 to Quit.\n";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";

        cin >> choice;
    }

    if(choice == 1)
    {

    }
    else
    {
        battle_over(constant.name);
    }

    //Send user to battle
    if(constant.class_ == 1)
    {
        combat_knight(constant,variable,monster,mon_name.name4);
    }
    else if(constant.class_ == 2)
    {
        combat_slayer(constant,variable,monster,mon_name.name4);
    }
    else
    {
        combat_priest(constant,variable,monster,mon_name.name4);
    }

    //Save the new data to the character file
    update.open(constant.name.c_str());

    update
    << constant.name << endl
    << constant.class_ << endl
    << constant.hp_a << endl
    << constant.mp_a << endl
    << constant.def_a << endl
    << constant.atk_a << endl
    << constant.hp << endl
    << constant.mp << endl
    << variable.level << endl
    << variable.exp << endl
    << variable.gold << endl
    << variable.point << endl
    << constant.def << endl
    << constant.atk << endl
    << variable.mp_potion << endl
    << variable.hp_potion << endl
    << constant.type;

    update.close();
    /*************************************************************************/


    /*****************************Boss Monster********************************/
    //Ask User if he wants to continue to fight monsters
    cout
    << "\n\n\n\n"
    << "Would you like to fight the Boss Monster?\n\n"
    << "Enter 1 to Continue. Enter 2 to Quit.\n";

    cin >> choice;

    while(choice < 1 || choice > 2)
    {
        cout << "\nPlease enter a valid choice: ";

        cin >> choice;
    }

    if(choice == 1)
    {

    }
    else
    {
        battle_over(constant.name);
    }

    //Indicate that this monster is a boss by changing monster.id to 1
    monster.id = 1;

    if(constant.class_ == 1)
    {
        combat_knight(constant,variable,monster,mon_name.name5);
    }
    else if(constant.class_ == 2)
    {
        combat_slayer(constant,variable,monster,mon_name.name5);
    }
    else
    {
        combat_priest(constant,variable,monster,mon_name.name5);
    }

    //Save the new data to the character file
    update.open(constant.name.c_str());

    update
    << constant.name << endl
    << constant.class_ << endl
    << constant.hp_a << endl
    << constant.mp_a << endl
    << constant.def_a << endl
    << constant.atk_a << endl
    << constant.hp << endl
    << constant.mp << endl
    << variable.level << endl
    << variable.exp << endl
    << variable.gold << endl
    << variable.point << endl
    << constant.def << endl
    << constant.atk << endl
    << variable.mp_potion << endl
    << variable.hp_potion << endl
    << constant.type;

    update.close();
    /*************************************************************************/

    //Tell user that this is the end of the dungeon
    cout << "\n\nYou have completed this dungeon.";

    //Send user to Battle Over Function to read from the updated character file
    //and send the data to Character Information Screen
    battle_over(constant.name);
}


//*****************************************************************************
//                           Battle Over Function                            **
//*****************************************************************************
//This function pretty much read all the character data from the character file
//and then convert the data to structured data, then send them to the Character
//Information Screen Function
void battle_over(string charname)
{
    //Get the data from the file and save it in the structure

    Constant_char_stats constant;

    Variable_char_stats variable;

    ifstream read;

    read.open(charname.c_str());

    read >> constant.name >> constant.class_ >> constant.hp_a >> constant.mp_a
    >> constant.def_a >> constant.atk_a >> constant.hp >> constant.mp
    >> variable.level >> variable.exp >> variable.gold >> variable.point
    >> constant.def >> constant.atk >> variable.mp_potion >> variable.hp_potion
    >> constant.type;

    //Then send the structure to Character Information Screen Function
    char_info_screen(constant,variable);
}

//*****************************************************************************
//                           Knight Combat Function                          **
//*****************************************************************************
//This Function allow Knights to fight monsters
void combat_knight(Constant_char_stats &constant,Variable_char_stats &variable,
Monster monster,string mon_name)
{
    cout << string(2,'\n');

    //Save user choice
    int choice;

    //Variable that breaks do-while loop
    int x;

    //Variable that breaks for-loop and send user back to Combat Preparation Function
    int y = 1;

    //Variable used to store various calculations
    int d;

    //Variable to limit the use of a buff skill to 1 use per battle
    //The variable if lower case L, not number 1
    int l = 1;

    //When boss monster's HP reaches a certain point, it will casts buffs on itself
    //These variables prevent it from keep stacking the buffs and making it impossible
    //to defeat.

    int b1 = 1;
    int b2 = 1;

    //Variables to store data that must stay constant in this function
    //Changes will be made to these variables rather than the variables in the
    //Constant structure. This will make programming other functions a lot easier.
    int hp_v = constant.hp;
    int mp_v = constant.mp;
    double def_v = constant.def;
    double atk_v = constant.atk;

    //Temporary variables to store monster statsto avoid any accidental changes
    //to monster stat variables. Also, this differentiates normal monster stats
    //and boss monster stats

    int mon_hp;
    int mon_atk;
    int mon_def;
    int mon_gold;
    int mon_exp;

    if(monster.id == 1)
    {
        mon_hp = monster.boss_hp;
        mon_atk = monster.boss_atk;
        mon_def = monster.boss_def;
        mon_gold = monster.boss_gold;
        mon_exp = monster.boss_exp;
    }
    else
    {
        mon_hp = monster.hp;
        mon_atk = monster.atk;
        mon_def = monster.def;
        mon_gold = monster.gold;
        mon_exp = monster.exp;
    }

    //Monster's HP is used as a test. If monster's hp reaches 0, user has killed the monster
    //and the loop and terminate
    for( ; mon_hp > 0; )
    {
        //Check to see if user has died.
        if(hp_v <= 0)
        {
            //If he died, take him to Death Screen Function.
            death_screen(constant,variable);
        }

        //If user is alive, continue the combat
        do
        {
            cout
            << endl
            << '\t' << constant.name << endl << endl << '\t'
            << "Knight" << "\t\t\t\t" << mon_name << endl << endl
            << '\t' << hp_v << " / " << constant.hp << " HP";

             if(monster.id == 1)
            {
                show_bar(constant.hp,constant.mp,monster.boss_hp,hp_v,mp_v,mon_hp);
            }
            else
            {
                show_bar(constant.hp,constant.mp,monster.hp,hp_v,mp_v,mon_hp);
            }

            cout
            << "\n\nAttack:\t\t" << atk_v << "\n\n"
            << "Defense:\t" << def_v << endl << endl
            << "Experience:\t" << variable.exp << " / ";

            show_next_level(variable.level);

            cout
            << string(2,'\n')
            << "\t\tYou are fighting " << mon_name << ".\n\n"
            << "\t1. Attack\t\t(Gains 20 MP)\n\n"
            << "\t2. Use Iron Will\t(Iron Will: Defense Buff   Cost: 30 MP)\n\n"
            << "\t3. Use Shield Bash\t(Shield Bash: 2X Attack   Cost: 40 MP)\n\n"
            << "\t4. Use Charge\t\t(Charge: 5X Attack   Cost: 65 MP)\n\n"
            << "\t5. Use HP Potion\t(Count: " << variable.hp_potion << ")\n\n"
            << "\t6. Use MP Potion\t(Count: " << variable.mp_potion << ")\n\n"
            << "\t7. Escape\n\n\n"
            << "Enter your choice: ";

            cin >> choice;

            while(choice < 1 || choice > 7)
            {
                cout << "\n\nPlease enter a valid choice: ";
                cin >> choice;
            }

            //User wants to use normal attack
            if(choice == 1)
            {
                //X = 0, meaning, user's turn will end after this is used
                x = 0;

                d = (atk_v - mon_def);

                //Players will inflict at least 1 damage no matter how high monster
                //defense is
                if(d <= 0)
                {
                    d = 1;
                }

                //Substract user damage from monster's HP
                mon_hp -= d;

                //Normal attack allows user to gain some MP
                mp_v += 20;

                //User can not gain more MP than his Max MP allows
                if(mp_v > constant.mp)
                {
                    mp_v = constant.mp;
                }

                cout
                << "\nYou have inflicted " << d << " damage to " << mon_name << ".\n\n";
            }
            //User wants to use Iron Will
            else if(choice == 2)
            {
                //If l = 0, then user has used Iron Will already
                if(l == 0)
                {
                    cout << "\nYou can use Iron Will only once while fighting the same monster.\n\n";

                    x = 1;
                }
                //If user has 30 MP or more, he can use the skill
                else if(mp_v >= 30)
                {
                    //This is a buff skill and it is still user's turn after the execution of this skill.
                    x = 1;

                    //This is the formula for Iron Will buff skill

                    d = (variable.level * variable.level * variable.level + variable.level * 50);

                    def_v += d;

                    //Subtract skill cost from user's MP pool
                    mp_v -= 30;

                    cout
                    << "\nYou have used Iron Will.\n"
                    << "\nYour defense has increased by " << d << " points.\n\n";

                    //User has used Iron Will, so change value of l to 0
                    l --;
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            else if(choice == 3)
            {
                //User should have 40 MP or more to use this skill
                if(mp_v >= 40)
                {
                    //This is an attack skill and user will lose his turn after using the skill
                    x = 0;

                    //This skill multiplies user's attack by 2
                    d = (atk_v * 2 - mon_def);

                    //If monster's defense is higher than user's attack, he will still inflict 1
                    //damage
                    if(d < 0)
                    {
                        d = 1;
                    }

                    //Subtract user damage from monster's HP
                    mon_hp -= d;

                    //Substract MP cost from user's MP pool
                    mp_v -= 40;

                    cout
                    << "\nYou have used Shield Bash.\n"
                    << "\nYou have inflicted " << d << " damage to "
                    << mon_name << '.';
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            else if(choice == 4)
            {
                if(mp_v >= 65)
                {
                    //Again, attack skills consumes user's turn
                    x = 0;

                    //This skill multiplies user's attack by 3
                    d = (atk_v * 5 - mon_def);

                    if(d < 0)
                    {
                        d = 1;
                    }

                    mon_hp -= d;

                    mp_v -= 65;

                    cout
                    << "\nYou have used Charge.\n"
                    << "\nYou have inflicted " << d << " damage to "
                    << mon_name << '.';
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            //User wants to use potion
            else if(choice == 5)
            {
                if(variable.hp_potion >= 1)
                {
                    //User will not lose his turn for drinking potions
                    x = 1;

                    //HP Potion heals 200 HP times character level^2 for Knights
                    d = (200 * variable.level * variable.level);

                    hp_v += d;

                    //Prevent user from healing over the max Hit Points
                    if(hp_v > constant.hp)
                    {
                        hp_v = constant.hp;
                    }

                    //Each time user uses potion, decrement potion count
                    variable.hp_potion --;

                    //Tell user how much he has recovered
                    cout
                    << "\nYou have used one HP Potion and healed "
                    << d << " HP.\n\n";
                }
                else
                {
                    cout << "\nYou do not have any HP Potions.\n\n";
                    x = 1;
                }
            }
            else if(choice == 6)
            {
                if(variable.mp_potion >= 1)
                {
                    x = 1;

                    //Knights gain 50 * character level for drinking MP Potion
                    d = (variable.level * 50);

                    mp_v += d;

                    //Prevent user from healing over the max MP
                    if(mp_v > constant.mp)
                    {
                        mp_v = constant.mp;
                    }

                    variable.mp_potion--;

                    cout
                    << "\nYou have used one MP Potion and gained "
                    << d << " MP.\n\n";
                }
                else
                {
                    cout << "\nYou do not have any MP Potions.\n\n";
                    x = 1;
                }
            }
            else
            {
                //User wants to escape rather than fighting
                y = 0;

                //Break the do-while loop
                x = 0;
            }

        } while(x == 1);

        if(y == 0)
        {
            //Break the for-loop and allow user to escape
            break;
        }

        //Allows boss monsters (id = 1) to use special buffs at certain HP.
        if(monster.id == 1)
        {
            //variable b2 ensures that boss does NOT keep stacking the buffs
            if(mon_hp < monster.boss_sp_hp2 && b2 == 1)
            {
                cout
                << "\n\n\nThe Boss is Infuriated\n"
                << "\nIts damage has increased.\n"
                << "\nIts defense has increased.\n";

                //Boss's stats are modified.
                mon_atk += monster.boss_sp_atk;
                mon_def += monster.boss_sp_def;

                //Decrement b2 after buffs are cast
                b2 --;
            }
            //Variable b1 ensures that boss does NOT keep stacking the buffs
            else if(mon_hp < monster.boss_sp_hp1 && b1 == 1)
            {
                cout
                << "\n\n\nThe Boss is Enraged.\n"
                << "\nIts damage has increased.\n";

                //Boss's stats are modified
                mon_atk += monster.boss_sp_atk;

                //Decrement b1 after the buff is cast
                b1 --;
            }
        }

        //Now, monster attacks. Calculate monster's damage to player
        d = (mon_atk - def_v);

        if(d <= 1)
        {
            //Monsters will do at least 1 damage no matter how high user's defense
            d = 1;
        }

        //Subtract Monster's Damage from user's HP
        hp_v -= d;

        cout
        << endl << endl << mon_name << " retaliates."
        << "\n\nYou have taken " << d
        << " damage from " << mon_name << "\n\n\n\n";
    }

    //Tell user that he has escaped
    if(y == 0)
    {
        cout << "\nYou have run away from " << mon_name << '.' << endl;
    }
    else
    {
        //User has killed the monster and earned the rewards
        variable.gold += mon_gold;
        variable.exp += mon_exp;

        cout
        << "\nYou have defeated " << mon_name << ".\n\n"
        << "\nYou have gained " << mon_exp << " experience and "
        << mon_gold << " gold.\n\n";

        //Check to see if user has gained enough experience to level up.
        level_up_screen(constant,variable);
    }
}

//*****************************************************************************
//                          Slayer Combat Function                           **
//*****************************************************************************
void combat_slayer(Constant_char_stats &constant,Variable_char_stats &variable,
Monster monster,string mon_name)
{
    cout << string(2,'\n');

    //Save user choice
    int choice;

    //Variable that breaks do-while loop
    int x;

    //Variable that breaks for-loop and send user back to Combat Preparation Function
    int y = 1;

    //Variable used to store various calculations
    int d;

    //Variable to limit the use of a buff skill to 1 use per battle
    //The variable if lower case L, not number 1
    int l = 1;

    //When boss monster's HP reaches a certain point, it will casts buffs on itself
    //These variables prevent it from keep stacking the buffs and making it impossible
    //to defeat.

    int b1 = 1;
    int b2 = 1;

    //Variables to store data that must stay constant in this function
    //Changes will be made to these variables rather than the variables in the
    //Constant structure. This will make programming other functions a lot easier.
    int hp_v = constant.hp;
    int mp_v = constant.mp;
    double def_v = constant.def;
    double atk_v = constant.atk;

    //Temporary variables to store monster statsto avoid any accidental changes
    //to monster stat variables. Also, this differentiates normal monster stats
    //and boss monster stats

    int mon_hp;
    int mon_atk;
    int mon_def;
    int mon_gold;
    int mon_exp;

    if(monster.id == 1)
    {
        mon_hp = monster.boss_hp;
        mon_atk = monster.boss_atk;
        mon_def = monster.boss_def;
        mon_gold = monster.boss_gold;
        mon_exp = monster.boss_exp;
    }
    else
    {
        mon_hp = monster.hp;
        mon_atk = monster.atk;
        mon_def = monster.def;
        mon_gold = monster.gold;
        mon_exp = monster.exp;
    }
    //Monster's HP is used as a test. If monster's hp reaches 0, user has killed the monster
    //and the loop and terminate
    for( ; mon_hp > 0; )
    {
        //Check to see if user has died.
        if(hp_v <= 0)
        {
            //If he died, take him to Death Screen Function.
            death_screen(constant,variable);
        }

        //If user is alive, continue the combat
        do
        {
            cout
            << endl
            << '\t' << constant.name << endl << endl << '\t'
            << "Slayer" << "\t\t\t\t" << mon_name << endl << endl
            << '\t' << hp_v << " / " << constant.hp << " HP";

            if(monster.id == 1)
            {
                show_bar(constant.hp,constant.mp,monster.boss_hp,hp_v,mp_v,mon_hp);
            }
            else
            {
                show_bar(constant.hp,constant.mp,monster.hp,hp_v,mp_v,mon_hp);
            }

            cout
            << "\n\nAttack:\t\t" << atk_v << "\n\n"
            << "Defense:\t" << def_v << endl << endl
            << "Experience:\t" << variable.exp << " / ";

            show_next_level(variable.level);

            cout
            << string(2,'\n')
            << "\t\tYou are fighting " << mon_name << ".\n\n"
            << "\t1. Attack\t\t(Gains 20 MP)\n\n"
            << "\t2. Use Berserk\t\t(Berserk: Attack Buff   Cost: 30 MP)\n\n"
            << "\t3. Use Slash\t\t(Slash: 3X Attack   Cost: 50 MP)\n\n"
            << "\t4. Use Penetration\t(Penetration: 4X Attack   Cost: 70 MP)\n\n"
            << "\t5. Use HP Potion\t(Count: " << variable.hp_potion << ")\n\n"
            << "\t6. Use MP Potion\t(Count: " << variable.mp_potion << ")\n\n"
            << "\t7. Escape\n\n\n"
            << "Enter your choice: ";

            cin >> choice;

            while(choice < 1 || choice > 7)
            {
                cout << "\n\nPlease enter a valid choice: ";
                cin >> choice;
            }

            //User wants to use normal attack
            if(choice == 1)
            {
                //X = 0, meaning, user's turn will end after this is used
                x = 0;

                d = (atk_v - mon_def);

                //Players will inflict at least 1 damage no matter how high monster
                //defense is
                if(d <= 0)
                {
                    d = 1;
                }

                //Substract user damage from monster's HP
                mon_hp -= d;

                //Normal attack allows user to gain some MP
                mp_v += 20;

                //User can not gain more MP than his Max MP allows
                if(mp_v > constant.mp)
                {
                    mp_v = constant.mp;
                }

                cout
                << "\nYou have inflicted " << d << " damage to " << mon_name << ".\n\n";
            }
            //User wants to use Berserk
            else if(choice == 2)
            {
                //If l = 0, then user has used Berserk already
                if(l == 0)
                {
                    cout << "\nYou can use Berserk only once while fighting the same monster.\n\n";

                    x = 1;
                }
                //If user has 30 MP or more, he can use the skill
                else if(mp_v >= 30)
                {
                    //This is a buff skill and it is still user's turn after the execution of this skill.
                    x = 1;

                    //This is the formula for Berserk buff skill

                    d = (variable.level * variable.level * variable.level * variable.level +
                         variable.level * 50);

                    atk_v += d;

                    //Subtract skill cost from user's MP pool
                    mp_v -= 30;

                    cout
                    << "\nYou have used Berserk.\n"
                    << "\nYour attack has increased by " << d << " points.\n\n";

                    //User has used Berserk, so change value of l to 0
                    l --;
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            else if(choice == 3)
            {
                //User should have 50 MP or more to use this skill
                if(mp_v >= 50)
                {
                    //This is an attack skill and user will lose his turn after using the skill
                    x = 0;

                    //This skill multiplies user's attack by 3
                    d = (atk_v * 3 - mon_def);

                    //If monster's defense is higher than user's attack, he will still inflict 1
                    //damage
                    if(d < 0)
                    {
                        d = 1;
                    }

                    //Subtract user damage from monster's HP
                    mon_hp -= d;

                    //Substract MP cost from user's MP pool
                    mp_v -= 50;

                    cout
                    << "\nYou have used Slash.\n"
                    << "\nYou have inflicted " << d << " damage to "
                    << mon_name << '.';
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            else if(choice == 4)
            {
                if(mp_v >= 70)
                {
                    //Again, attack skills consumes user's turn
                    x = 0;

                    //This skill multiplies user's attack by 4
                    d = (atk_v * 4 - mon_def);

                    if(d < 0)
                    {
                        d = 1;
                    }

                    mon_hp -= d;

                    mp_v -= 70;

                    cout
                    << "\nYou have used Penetration.\n"
                    << "\nYou have inflicted " << d << " damage to "
                    << mon_name << '.';
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            //User wants to use potion
            else if(choice == 5)
            {
                if(variable.hp_potion >= 1)
                {
                    //User will not lose his turn for drinking potions
                    x = 1;

                    //HP Potion heals 250 HP times character level^2 for Slayers
                    d = (250 * variable.level * variable.level);

                    hp_v += d;

                    //Prevent user from healing over the max Hit Points
                    if(hp_v > constant.hp)
                    {
                        hp_v = constant.hp;
                    }

                    //Each time user uses potion, decrement potion count
                    variable.hp_potion --;

                    //Tell user how much he has recovered
                    cout
                    << "\nYou have used one HP Potion and healed "
                    << d << " HP.\n\n";
                }
                else
                {
                    cout << "\nYou do not have any HP Potions.\n\n";
                    x = 1;
                }
            }
            else if(choice == 6)
            {
                if(variable.mp_potion >= 1)
                {
                    x = 1;

                    //Slayers gain 50 * character level for drinking MP Potion
                    d = (variable.level * 50);

                    mp_v += d;

                    //Prevent user from healing over the max MP
                    if(mp_v > constant.mp)
                    {
                        mp_v = constant.mp;
                    }

                    variable.mp_potion--;

                    cout
                    << "\nYou have used one MP Potion and gained "
                    << d << " MP.\n\n";
                }
                else
                {
                    cout << "\nYou do not have any MP Potions.\n\n";
                    x = 1;
                }
            }
            else
            {
                //User wants to escape rather than fighting
                y = 0;

                //Break the do-while loop
                x = 0;
            }

        } while(x == 1);

        if(y == 0)
        {
            //Break the for-loop and allow user to escape
            break;
        }

        //Allows boss monsters (id = 1) to use special buffs at certain HP.
        if(monster.id == 1)
        {
            //variable b2 ensures that boss does NOT keep stacking the buffs
            if(mon_hp < monster.boss_sp_hp2 && b2 == 1)
            {
                cout
                << "\n\n\nThe Boss is Infuriated\n"
                << "\nIts damage has increased.\n"
                << "\nIts defense has increased.\n";

                //Boss's stats are modified.
                mon_atk += monster.boss_sp_atk;
                mon_def += monster.boss_sp_def;

                //Decrement b2 after buffs are cast
                b2 --;
            }
            //Variable b1 ensures that boss does NOT keep stacking the buffs
            else if(mon_hp < monster.boss_sp_hp1 && b1 == 1)
            {
                cout
                << "\n\n\nThe Boss is Enraged.\n"
                << "\nIts damage has increased.\n";

                //Boss's stats are modified
                mon_atk += monster.boss_sp_atk;

                //Decrement b1 after the buff is cast
                b1 --;
            }
        }

        //Now, monster attacks. Calculate monster's damage to player
        d = (mon_atk - def_v);

        if(d <= 1)
        {
            //Monsters will do at least 1 damage no matter how high user's defense
            d = 1;
        }

        //Subtract Monster's Damage from user's HP
        hp_v -= d;

        cout
        << endl << endl << mon_name << " retaliates."
        << "\n\nYou have taken " << d
        << " damage from " << mon_name << "\n\n\n\n";
    }

    //Tell user that he has escaped
    if(y == 0)
    {
        cout << "\nYou have run away from " << mon_name << '.' << endl;
    }
    else
    {
        //User has killed the monster and earned the rewards
        variable.gold += mon_gold;
        variable.exp += mon_exp;

        cout
        << "\nYou have defeated " << mon_name << ".\n\n"
        << "\nYou have gained " << mon_exp << " experience and "
        << mon_gold << " gold.\n\n";

        //Check to see if user has gained enough experience to level up.
        level_up_screen(constant,variable);
    }
}

//*****************************************************************************
//                           Priest Combat Function                          **
//*****************************************************************************
void combat_priest(Constant_char_stats &constant,Variable_char_stats &variable,
Monster monster,string mon_name)
{
    cout << string(2,'\n');

    //Save user choice
    int choice;

    //Variable that breaks do-while loop
    int x;

    //Variable that breaks for-loop and send user back to Combat Preparation Function
    int y = 1;

    //Variable used to store various calculations
    int d;

    //Variable to limit the use of a buff skill to 1 use per battle
    //The variable is lower case L, not number 1
    int l = 1;

    //These variables indicate that a special ability has been used. Depending on the value of
    //These variables, a specific conditions will apply.
    int salvation = 0;
    int meditation = 0;

    //When boss monster's HP reaches a certain point, it will casts buffs on itself
    //These variables prevent it from keep stacking the buffs and making it impossible
    //to defeat.

    int b1 = 1;
    int b2 = 1;

    //Variables to store data that must stay constant in this function
    //Changes will be made to these variables rather than the variables in the
    //Constant structure. This will make programming other functions a lot easier.
    int hp_v = constant.hp;
    int mp_v = constant.mp;
    double def_v = constant.def;
    double atk_v = constant.atk;

    //Temporary variables to store monster stats to avoid any accidental changes to
    //monster stat variables. Also, this differentiates normal monster stats and boss
    //monster stats
    int mon_hp;
    int mon_atk;
    int mon_def;
    int mon_gold;
    int mon_exp;

    if(monster.id == 1)
    {
        mon_hp = monster.boss_hp;
        mon_atk = monster.boss_atk;
        mon_def = monster.boss_def;
        mon_gold = monster.boss_gold;
        mon_exp = monster.boss_exp;
    }
    else
    {
        mon_hp = monster.hp;
        mon_atk = monster.atk;
        mon_def = monster.def;
        mon_gold = monster.gold;
        mon_exp = monster.exp;
    }

    //Monster's HP is used as a test. If monster's hp reaches 0, user has killed the monster
    //and the loop and terminate
    for( ; mon_hp > 0; )
    {
        //Check to see if user has died.
        if(hp_v <= 0)
        {
            //If he died, take him to Death Screen Function.
            death_screen(constant,variable);
        }

        //If user is alive, continue the combat
        do
        {
            cout
            << endl
            << '\t' << constant.name << endl << endl << '\t'
            << "Priest" << "\t\t\t\t" << mon_name << endl << endl
            << '\t' << hp_v << " / " << constant.hp << " HP";

            if(monster.id == 1)
            {
                show_bar(constant.hp,constant.mp,monster.boss_hp,hp_v,mp_v,mon_hp);
            }
            else
            {
                 show_bar(constant.hp,constant.mp,monster.hp,hp_v,mp_v,mon_hp);
            }

            cout
            << "\n\nAttack:\t\t" << atk_v << "\n\n"
            << "Defense:\t" << def_v
            << "\n\n\nEffect: MP regeneration after each turn. (Priest Only)"
            << endl << endl
            << "Experience:\t" << variable.exp << " / ";

            show_next_level(variable.level);

            cout
            << string(2,'\n')
            << "\tYou are fighting " << mon_name << ".\n\n"
            << "1. Attack\t\t(Gains 50 MP)\n\n"
            << "2. Use Salvation\t(Salvation: Mana Shield   Cost: 150 MP)\n\n"
            << "3. Use Meditation\t(Meditation: Increase MP Regeneration   Cost: 150 MP)\n\n"
            << "4. Use Redemption\t(Redemption: Defense Buff   Cost: 150 MP)\n\n"
            << "5. Use Heaven's Fist\t(Heaven's Fist: 2X Attack   Cost: 100 MP)\n\n"
            << "6. Use Last Judgment\t(Last Judgment: 4X Attack   Cost: 150 MP)\n\n"
            << "7. Use HP Potion\t(Count: " << variable.hp_potion << ")\n\n"
            << "8. Use MP Potion\t(Count: " << variable.mp_potion << ")\n\n"
            << "9. Escape\n\n\n"
            << "Enter your choice: ";

            cin >> choice;

            while(choice < 1 || choice > 9)
            {
                cout << "\n\nPlease enter a valid choice: ";
                cin >> choice;
            }

            //User wants to use normal attack
            if(choice == 1)
            {
                //X = 0, meaning, user's turn will end after this is used
                x = 0;

                d = (atk_v - mon_def);

                //Players will inflict at least 1 damage no matter how high monster
                //defense is
                if(d <= 0)
                {
                    d = 1;
                }

                //Substract user damage from monster's HP
                mon_hp -= d;

                //Normal attack allows user to gain some MP
                mp_v += 50;

                //User can not gain more MP than his Max MP allows
                if(mp_v > constant.mp)
                {
                    mp_v = constant.mp;
                }

                cout
                << "\nYou have inflicted " << d << " damage to " << mon_name << ".\n\n";
            }
            //User wants to use Salvation
            else if(choice == 2)
            {
                //If salvation = 1, then user has used Salvation already
                if(salvation == 1)
                {
                    cout << "\nYou can use Salvation only once while fighting the same monster.\n\n";

                    x = 1;
                }
                //If user has 150 MP or more, he can use the skill.
                else if(mp_v >= 150)
                {
                    //This is a buff skill and it is still user's turn after the execution of this skill.
                    x = 1;

                    //Turn on Salvation
                    salvation = 1;

                    //Subtract skill cost from user's MP pool
                    mp_v -= 150;

                    cout
                    << "\nYou have used Salvation.\n"
                    << "\nYour MP absorbs damage now\n\n";
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            //User wants to use Meditation
            else if(choice == 3)
            {
                if(meditation == 1)
                {
                    cout << "\nYou can use meditation only once while fighting the same monster.\n\n";

                    x = 1;
                }
                //User should have 150 MP or more to use this skill
                if(mp_v >= 150)
                {
                    //This is a buff skill and user will not lose his turn after using the skill
                    x = 1;

                    //Turn on Meditation
                    meditation = 1;

                    //Substract MP cost from user's MP pool
                    mp_v -= 150;

                    cout
                    << "\nYou have used Meditation.\n"
                    << "\nYour MP Renegeration has increased.\n\n";
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            //User wants to use Redemption
            else if(choice == 4)
            {
                //If l = 0, then user has used Redemption already
                if(l == 0)
                {
                    cout << "\nYou can use Redemption only once while fighting the same monster.\n\n";

                    x = 1;
                }
                //If user has 150 MP or more, he can use the skill
                else if(mp_v >= 150)
                {
                    //This is a buff skill and it is still user's turn after the execution of this skill.
                    x = 1;

                    //This is the formula for Redemption

                    d = (variable.level * variable.level * variable.level + variable.level * 10);

                    def_v += d;

                    //Subtract skill cost from user's MP pool
                    mp_v -= 150;

                    cout
                    << "\nYou have used Redemption.\n"
                    << "\nYour defense has increased by " << d << " points.\n\n";

                    //User has used Redemption, so change value of l to 0
                    l --;
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            //User wants to use Heaven's Fist
            else if(choice == 5)
            {
                if(mp_v >= 100)
                {
                    //Attack skills consumes user's turn
                    x = 0;

                    //This skill multiplies user's attack by 2
                    d = (atk_v * 2 - mon_def);

                    if(d < 0)
                    {
                        d = 1;
                    }

                    mon_hp -= d;

                    mp_v -= 100;

                    cout
                    << "\nYou have used Heaven's Fist.\n"
                    << "\nYou have inflicted " << d << " damage to "
                    << mon_name << '.';
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            //User wants to use Last Judgment
            else if(choice == 6)
            {
                if(mp_v >= 150)
                {
                    //Attack skills consumes user's turn
                    x = 0;

                    //This skill multiplies user's attack by 4
                    d = (atk_v * 4 - mon_def);

                    if(d < 0)
                    {
                        d = 1;
                    }

                    mon_hp -= d;

                    mp_v -= 150;

                    cout
                    << "\nYou have used Last Judgment.\n"
                    << "\nYou have inflicted " << d << " damage to "
                    << mon_name << '.';
                }
                else
                {
                    cout << "\nYou do not have enough Mana.\n\n";
                    x = 1;
                }
            }
            //User wants to use potion
            else if(choice == 7)
            {
                if(variable.hp_potion >= 1)
                {
                    //User will not lose his turn for drinking potions
                    x = 1;

                    //HP Potion heals 50 HP times character level for Priests
                    d = (50 * variable.level);

                    hp_v += d;

                    //Prevent user from healing over the max Hit Points
                    if(hp_v > constant.hp)
                    {
                        hp_v = constant.hp;
                    }

                    //Each time user uses potion, decrement potion count
                    variable.hp_potion --;

                    //Tell user how much he has recovered
                    cout
                    << "\nYou have used one HP Potion and healed "
                    << d << " HP.\n\n";
                }
                else
                {
                    cout << "\nYou do not have any HP Potions.\n\n";
                    x = 1;
                }
            }
            else if(choice == 8)
            {
                if(variable.mp_potion >= 1)
                {
                    x = 1;

                    //Priests gain 230 * character level^2 for drinking MP Potion
                    d = (variable.level * variable.level * 230);

                    mp_v += d;

                    //Prevent user from healing over the max MP
                    if(mp_v > constant.mp)
                    {
                        mp_v = constant.mp;
                    }

                    variable.mp_potion--;

                    cout
                    << "\nYou have used one MP Potion and gained "
                    << d << " MP.\n\n";
                }
                else
                {
                    cout << "\nYou do not have any MP Potions.\n\n";
                    x = 1;
                }
            }
            else
            {
                //User wants to escape rather than fighting
                y = 0;

                //Break the do-while loop
                x = 0;
            }

        } while(x == 1);

        if(y == 0)
        {
            //Break the for-loop and allow user to escape
            break;
        }

        //Allows boss monsters (id = 1) to use special buffs at certain HP.
        if(monster.id == 1)
        {
            //variable b2 ensures that boss does NOT keep stacking the buffs
            if(mon_hp < monster.boss_sp_hp2 && b2 == 1)
            {
                cout
                << "\n\n\nThe Boss is Infuriated\n"
                << "\nIts damage has increased.\n"
                << "\nIts defense has increased.\n";

                //Boss's stats are modified.
                mon_atk += monster.boss_sp_atk;
                mon_def += monster.boss_sp_def;

                //Decrement b2 after buffs are cast
                b2 --;
            }
            //Variable b1 ensures that boss does NOT keep stacking the buffs
            else if(mon_hp < monster.boss_sp_hp1 && b1 == 1)
            {
                cout
                << "\n\n\nThe Boss is Enraged.\n"
                << "\nIts damage has increased.\n";

                //Boss's stats are modified
                mon_atk += monster.boss_sp_atk;

                //Decrement b1 after the buff is cast
                b1 --;
            }
        }

        //Now, monster attacks. Calculate monster's damage to player
        d = (mon_atk - def_v);

        if(d <= 1)
        {
            //Monsters will do at least 1 damage no matter how high user's defense
            d = 1;
        }

        //If Salvation is activated, deal monster damage to user's MP pool
        if(salvation == 1)
        {
            //Created a variable to see if monster's damage is greater than player's
            //remaining MP pool
            int i = d - mp_v;

            if(i > 0)
            {
                //Leftover damage goes to user HP
                hp_v -= i;

                //MP is completely drained
                mp_v = 0;

                cout
                << "\nYour Mana Shield has been penetrated and you took " << i << " damage.\n\n"
                << "\nYou may cast Salvation again to activate Mana Shield.\n\n\n\n";

                salvation = 0;
            }
            else
            {
                mp_v -= d;

                cout
                << endl << endl << mon_name << " retaliates."
                << "\n\nYou have absorbed " << d
                << " damage from " << mon_name << "\n\n\n\n";
            }
        }
        //If Salvation is not activated, just deal the damange to user HP
        else
        {
            //Subtract Monster's Damage from user's HP
            hp_v -= d;

            cout
            << endl << endl << mon_name << " retaliates."
            << "\n\nYou have taken " << d
            << " damage from " << mon_name << "\n\n\n\n";
        }

        //Effect of Meditation: user gains 70 MP after each turn
        if(meditation == 1)
        {
            mp_v += 30;

            //user's MP pool cannot go over the max
            if(mp_v > constant.mp)
            {
                mp_v = constant.mp;
            }
        }
        //User gains 20 MP after each turn without Meditation on
        else
        {
            mp_v += 20;

            //Cannot go over the max MP pool
            if(mp_v > constant.mp)
            {
                mp_v = constant.mp;
            }
        }
    }

    //Tell user that he has escaped
    if(y == 0)
    {
        cout << "\nYou have run away from " << mon_name << '.' << endl;
    }
    else
    {
        //User has killed the monster and earned the rewards
        variable.gold += mon_gold;
        variable.exp += mon_exp;

        cout
        << "\nYou have defeated " << mon_name << ".\n\n"
        << "\nYou have gained " << mon_exp << " experience and "
        << mon_gold << " gold.\n\n";

        //Check to see if user has gained enough experience to level up.
        level_up_screen(constant,variable);
    }
}

//*****************************************************************************
//                               Level-Up Function                           **
//*****************************************************************************
void level_up_screen(Constant_char_stats &constant,Variable_char_stats &variable)
{
    //Store user choice
    int choice;

    //This variable determines if user has leveled up. If he did, he has the
    //choice to go back to character menu and spend attribute points
    int x = 0;

    //This variable is used to halt any level-ups after level 10
    //If y is equal to 1, player will no longer level up

    int y = 0;
    if(variable.level == 1)
    {
        if(variable.exp > 5000)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 10 attribute points.\n\n"
            << "You are now Level 2.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 10;
        }
    }
    else if(variable.level == 2)
    {
        if(variable.exp > 10000)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 15 attribute points.\n\n"
            << "You are now Level 3.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 15;
        }
    }
    else if(variable.level == 3)
    {
        if(variable.exp > 50000)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 20 attribute points.\n\n"
            << "You are now Level 4.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 20;
        }
    }
    else if(variable.level == 4)
    {
        if(variable.exp > 100000)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 25 attribute points.\n\n"
            << "You are now Level 5.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 25;
        }
    }
    else if(variable.level == 5)
    {
        if(variable.exp > 500000)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 30 attribute points.\n\n"
            << "You are now Level 6.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 30;
        }
    }
    else if(variable.level == 6)
    {
        if(variable.exp > 1000000)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 35 attribute points.\n\n"
            << "You are now Level 7.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 35;
        }
    }
    else if(variable.level == 7)
    {
        if(variable.exp > 5000000)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 40 attribute points.\n\n"
            << "You are now Level 8.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 40;
        }
    }
    else if(variable.level == 8)
    {
        if(variable.exp > 10000000)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 45 attribute points.\n\n"
            << "You are now Level 9.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 45;
        }
    }
    else if(variable.level == 9)
    {
        if(variable.exp > 50000000 && y == 0)
        {
            x = 1;

            cout
            << "\n\nYou have leveled up!\n\n"
            << "You gained 100 attribute points.\n\n"
            << "You are now Level 10.\n\n"
            << "Congratulations, you have reached the max level.\n\n"
            << "Your character stats have increased.\n\n";

            //Each class get a bonus to stats after each level-up
            if(constant.class_ == 1)
            {
                constant.hp += 2000;
                constant.mp += 20;
                constant.def += 300;
                constant.atk += 350;
            }
            else if(constant.class_ == 2)
            {
                constant.hp += 2500;
                constant.mp += 20;
                constant.def += 250;
                constant.atk += 500;
            }
            else
            {
                constant.hp += 100;
                constant.mp += 1500;
                constant.def += 250;
                constant.atk += 400;
            }

            variable.level ++;
            variable.point += 100;

            //Ensures that the user no longer levels up after level 10
            y = 1;
        }
    }
    else
    {

    }

    //Save the new data to the character file
    ofstream update;

    update.open(constant.name.c_str());

    update
    << constant.name << endl
    << constant.class_ << endl
    << constant.hp_a << endl
    << constant.mp_a << endl
    << constant.def_a << endl
    << constant.atk_a << endl
    << constant.hp << endl
    << constant.mp << endl
    << variable.level << endl
    << variable.exp << endl
    << variable.gold << endl
    << variable.point << endl
    << constant.def << endl
    << constant.atk << endl
    << variable.mp_potion << endl
    << variable.hp_potion << endl
    << constant.type;

    update.close();

    if(x == 1)
    {
        cout
        << "\n\nTo continue without spending attribute points, enter 1.\n\n"
        << "To leave the dungeon and spend attribute points right now, enter 2.\n";

        cin >> choice;

        while(choice < 1 || choice > 2)
        {
            cout << "Please enter a valid choice: ";

            cin >> choice;
        }

        if(choice == 1)
        {

        }
        else
        {
            add_attribute(constant,variable);
        }
    }
}

//*****************************************************************************
//                               Death Function                              **
//*****************************************************************************
void death_screen(Constant_char_stats constant,Variable_char_stats variable)
{
    if(constant.type == 2)
    {
        cout
        << "\nYou have died...\n"
        << "Because this is a hardcore character, it can no longer be played.\n"
        << "Your Deeds of Valor Will Be Remembered.\n";

        constant.type = 0;

        ofstream update;
        update.open(constant.name.c_str());

        update
        << constant.name << endl
        << constant.class_ << endl
        << constant.hp_a << endl
        << constant.mp_a << endl
        << constant.def_a << endl
        << constant.atk_a << endl
        << constant.hp << endl
        << constant.mp << endl
        << variable.level << endl
        << variable.exp << endl
        << variable.gold << endl
        << variable.point << endl
        << constant.def << endl
        << constant.atk << endl
        << variable.mp_potion << endl
        << variable.hp_potion << endl
        << constant.type;

        update.close();

        menu();
    }
    else
    {
        cout
        << "\nYou have died...\n\n"
        << "The monster took all of your gold.\n\n"
        << "You will be resurrected!";

        variable.gold = 0;


        //Update the character file
        ofstream update;
        update.open(constant.name.c_str());

        update
        << constant.name << endl
        << constant.class_ << endl
        << constant.hp_a << endl
        << constant.mp_a << endl
        << constant.def_a << endl
        << constant.atk_a << endl
        << constant.hp << endl
        << constant.mp << endl
        << variable.level << endl
        << variable.exp << endl
        << variable.gold << endl
        << variable.point << endl
        << constant.def << endl
        << constant.atk << endl
        << variable.mp_potion << endl
        << variable.hp_potion << endl
        << constant.type;

        update.close();

        char_info_screen(constant,variable);
    }
}

//*****************************************************************************
//                       Health Bar Array Function                           **
//*****************************************************************************
//This function will display the health bars of user and monster
void show_bar(int constant_hp,int constant_mp,int mon_hp,int hp_v,int mp_v,int mon_hp_v)
{
    cout << endl;

    //Array for creating health bar of user
    char bar1[3][12] = {{'-','-','-','-','-','-','-','-','-','-','-','-'},
                       {'|','*','*','*','*','*','*','*','*','*','*','|'},
                       {'-','-','-','-','-','-','-','-','-','-','-','-'}};

    //Array for creating MP bar of user
    char bar2[3][12] = {{'-','-','-','-','-','-','-','-','-','-','-','-'},
                       {'|','*','*','*','*','*','*','*','*','*','*','|'},
                       {'-','-','-','-','-','-','-','-','-','-','-','-'}};

    //Array for creating health bar of monster
    char bar3[3][12] = {{'-','-','-','-','-','-','-','-','-','-','-','-'},
                       {'|','*','*','*','*','*','*','*','*','*','*','|'},
                       {'-','-','-','-','-','-','-','-','-','-','-','-'}};


    //Variables for calculating percentage for bar1
    double ninety_hp = (constant_hp * 90 / 100);
    double eighty_hp = (constant_hp * 80 / 100);
    double seventy_hp = (constant_hp * 70 / 100);
    double sixty_hp = (constant_hp * 60 / 100);
    double fifty_hp = (constant_hp * 50 / 100);
    double fourty_hp = (constant_hp * 40 / 100);
    double thirty_hp = (constant_hp * 30 / 100);
    double twenty_hp = (constant_hp * 20 / 100);
    double ten_hp = (constant_hp * 10 / 100);

    //Variables for calculating percentage for bar2
    double ninety_mp = (constant_mp * 90 / 100);
    double eighty_mp = (constant_mp * 80 / 100);
    double seventy_mp = (constant_mp * 70 / 100);
    double sixty_mp = (constant_mp * 60 / 100);
    double fifty_mp = (constant_mp * 50 / 100);
    double fourty_mp = (constant_mp * 40 / 100);
    double thirty_mp = (constant_mp * 30 / 100);
    double twenty_mp = (constant_mp * 20 / 100);
    double ten_mp = (constant_mp * 10 / 100);

    //Variables for calculating percentage for bar3
    double ninety_mon = (mon_hp * 90 / 100);
    double eighty_mon = (mon_hp * 80 / 100);
    double seventy_mon = (mon_hp * 70 / 100);
    double sixty_mon = (mon_hp * 60 / 100);
    double fifty_mon = (mon_hp * 50 / 100);
    double fourty_mon = (mon_hp * 40 / 100);
    double thirty_mon = (mon_hp * 30 / 100);
    double twenty_mon = (mon_hp * 20 / 100);
    double ten_mon = (mon_hp * 10 / 100);

    if(hp_v > ninety_hp)
    {

    }
    else if(hp_v > eighty_hp)
    {
        bar1[1][10] = ' ';
    }
    else if(hp_v > seventy_hp)
    {
        bar1[1][10] = ' ';
        bar1[1][9] = ' ';
    }
    else if(hp_v > sixty_hp)
    {
        bar1[1][10] = ' ';
        bar1[1][9] = ' ';
        bar1[1][8] = ' ';
    }
    else if(hp_v > fifty_hp)
    {
        bar1[1][10] = ' ';
        bar1[1][9] = ' ';
        bar1[1][8] = ' ';
        bar1[1][7] = ' ';
    }
    else if(hp_v > fourty_hp)
    {
        bar1[1][10] = ' ';
        bar1[1][9] = ' ';
        bar1[1][8] = ' ';
        bar1[1][7] = ' ';
        bar1[1][6] = ' ';
    }
    else if(hp_v > thirty_hp)
    {
        bar1[1][10] = ' ';
        bar1[1][9] = ' ';
        bar1[1][8] = ' ';
        bar1[1][7] = ' ';
        bar1[1][6] = ' ';
        bar1[1][5] = ' ';
    }
    else if(hp_v > twenty_hp)
    {
        bar1[1][10] = ' ';
        bar1[1][9] = ' ';
        bar1[1][8] = ' ';
        bar1[1][7] = ' ';
        bar1[1][6] = ' ';
        bar1[1][5] = ' ';
        bar1[1][4] = ' ';
    }
    else if(hp_v > ten_hp)
    {
        bar1[1][10] = ' ';
        bar1[1][9] = ' ';
        bar1[1][8] = ' ';
        bar1[1][7] = ' ';
        bar1[1][6] = ' ';
        bar1[1][5] = ' ';
        bar1[1][4] = ' ';
        bar1[1][3] = ' ';
    }
    else if(hp_v >= 0)
    {
        bar1[1][10] = ' ';
        bar1[1][9] = ' ';
        bar1[1][8] = ' ';
        bar1[1][7] = ' ';
        bar1[1][6] = ' ';
        bar1[1][5] = ' ';
        bar1[1][4] = ' ';
        bar1[1][3] = ' ';
        bar1[1][2] = ' ';
    }

    if(mp_v > ninety_mp)
    {

    }
    else if(mp_v > eighty_mp)
    {
        bar2[1][10] = ' ';
    }
    else if(mp_v > seventy_mp)
    {
        bar2[1][10] = ' ';
        bar2[1][9] = ' ';
    }
    else if(mp_v > sixty_mp)
    {
        bar2[1][10] = ' ';
        bar2[1][9] = ' ';
        bar2[1][8] = ' ';
    }
    else if(mp_v > fifty_mp)
    {
        bar2[1][10] = ' ';
        bar2[1][9] = ' ';
        bar2[1][8] = ' ';
        bar2[1][7] = ' ';
    }
    else if(mp_v > fourty_mp)
    {
        bar2[1][10] = ' ';
        bar2[1][9] = ' ';
        bar2[1][8] = ' ';
        bar2[1][7] = ' ';
        bar2[1][6] = ' ';
    }
    else if(mp_v > thirty_mp)
    {
        bar2[1][10] = ' ';
        bar2[1][9] = ' ';
        bar2[1][8] = ' ';
        bar2[1][7] = ' ';
        bar2[1][6] = ' ';
        bar2[1][5] = ' ';
    }
    else if(mp_v > twenty_mp)
    {
        bar2[1][10] = ' ';
        bar2[1][9] = ' ';
        bar2[1][8] = ' ';
        bar2[1][7] = ' ';
        bar2[1][6] = ' ';
        bar2[1][5] = ' ';
        bar2[1][4] = ' ';
    }
    else if(mp_v > ten_mp)
    {
        bar2[1][10] = ' ';
        bar2[1][9] = ' ';
        bar2[1][8] = ' ';
        bar2[1][7] = ' ';
        bar2[1][6] = ' ';
        bar2[1][5] = ' ';
        bar2[1][4] = ' ';
        bar2[1][3] = ' ';
    }
    else if(mp_v >= 0)
    {
        bar2[1][10] = ' ';
        bar2[1][9] = ' ';
        bar2[1][8] = ' ';
        bar2[1][7] = ' ';
        bar2[1][6] = ' ';
        bar2[1][5] = ' ';
        bar2[1][4] = ' ';
        bar2[1][3] = ' ';
        bar2[1][2] = ' ';
    }

    if(mon_hp_v > ninety_mon)
    {

    }
    else if(mon_hp_v > eighty_mon)
    {
        bar3[1][10] = ' ';
    }
    else if(mon_hp_v > seventy_mon)
    {
        bar3[1][10] = ' ';
        bar3[1][9] = ' ';
    }
    else if(mon_hp_v > sixty_mon)
    {
        bar3[1][10] = ' ';
        bar3[1][9] = ' ';
        bar3[1][8] = ' ';
    }
    else if(mon_hp_v > fifty_mon)
    {
        bar3[1][10] = ' ';
        bar3[1][9] = ' ';
        bar3[1][8] = ' ';
        bar3[1][7] = ' ';
    }
    else if(mon_hp_v > fourty_mon)
    {
        bar3[1][10] = ' ';
        bar3[1][9] = ' ';
        bar3[1][8] = ' ';
        bar3[1][7] = ' ';
        bar3[1][6] = ' ';
    }
    else if(mon_hp_v > thirty_mon)
    {
        bar3[1][10] = ' ';
        bar3[1][9] = ' ';
        bar3[1][8] = ' ';
        bar3[1][7] = ' ';
        bar3[1][6] = ' ';
        bar3[1][5] = ' ';
    }
    else if(mon_hp_v > twenty_mon)
    {
        bar3[1][10] = ' ';
        bar3[1][9] = ' ';
        bar3[1][8] = ' ';
        bar3[1][7] = ' ';
        bar3[1][6] = ' ';
        bar3[1][5] = ' ';
        bar3[1][4] = ' ';
    }
    else if(mon_hp_v > ten_mon)
    {
        bar3[1][10] = ' ';
        bar3[1][9] = ' ';
        bar3[1][8] = ' ';
        bar3[1][7] = ' ';
        bar3[1][6] = ' ';
        bar3[1][5] = ' ';
        bar3[1][4] = ' ';
        bar3[1][3] = ' ';
    }
    else if(mon_hp_v >= 0)
    {
        bar3[1][10] = ' ';
        bar3[1][9] = ' ';
        bar3[1][8] = ' ';
        bar3[1][7] = ' ';
        bar3[1][6] = ' ';
        bar3[1][5] = ' ';
        bar3[1][4] = ' ';
        bar3[1][3] = ' ';
        bar3[1][2] = ' ';
    }

    cout << '\t';

    for(int col = 0; col < 12; col++)
    {
        cout << bar1[0][col];
    }

    cout << "\t\t\t";

    for(int col = 0; col < 12; col++)
    {
        cout << bar3[0][col];
    }

    cout << endl << '\t';

    for(int col = 0; col < 12; col++)
    {
        cout << bar1[1][col];
    }

    cout << "\t\t\t";

    for(int col = 0; col < 12; col++)
    {
        cout << bar3[1][col];
    }

    cout << endl << '\t';

    for(int col = 0; col < 12; col++)
    {
        cout << bar1[2][col];
    }

    cout << "\t\t\t";

    for(int col = 0; col < 12; col++)
    {
        cout << bar3[2][col];
    }

    cout << endl << endl;

    cout << '\t' << mp_v << " / " << constant_mp << " MP" << endl;

    for(int row = 0; row < 3; row++)
    {
        cout << '\t';

        for(int col = 0; col < 12; col++)
        {
            cout << bar2[row][col];
        }

        cout << endl;
    }

    cout << endl << endl;
}

//*****************************************************************************
//                  Show Required Experience Function                        **
//*****************************************************************************
//Function that shows how much experience user needs to the next level
//in form of Current Experience / Required Experience
void show_next_level(short char_level)
{
    if(char_level == 1)
    {
        cout << "5000\n\n\n";
    }
    else if(char_level == 2)
    {
        cout << "10000\n\n\n";
    }
    else if(char_level == 3)
    {
        cout << "50000\n\n\n";
    }
    else if(char_level == 4)
    {
        cout << "100000\n\n\n";
    }
    else if(char_level == 5)
    {
        cout << "500000\n\n\n";
    }
    else if(char_level == 6)
    {
        cout << "1000000\n\n\n";
    }
    else if(char_level == 7)
    {
        cout << "5000000\n\n\n";
    }
    else if(char_level == 8)
    {
        cout << "10000000\n\n\n";
    }
    else if(char_level == 9)
    {
        cout << "50000000\n\n\n";
    }
    else
    {
        cout << "MAX\n\n\n";
    }
}


/*********************************END OF PROGRAM**********************************/
