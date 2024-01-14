
        self.graphismes = graphismes or []
        self.choix = choix or {}

    def ajouter_choix(self, choix, etape_suivante):
        self.choix[choix] = etape_suivante

class GestionnaireChoix:
    @staticmethod
    def valider_choix(choix_possibles, choix_utilisateur):
        return choix_utilisateur in choix_possibles

class StatistiquesSuivi:
    def __init__(self):
        self.nombres_choix_faits = 0
        self.choix_par_etape = {}

    def enregistrer_choix(self, etape, choix_utilisateur):
        self.nombres_choix_faits += 1
        if etape.description not in self.choix_par_etape:
            self.choix_par_etape[etape.description] = {}
        self.choix_par_etape[etape.description][choix_utilisateur] = self.choix_par_etape[etape.description].get(choix_utilisateur, 0) + 1

    def afficher_statistiques(self):
        print("Statistiques de jeu:")
        print(f"Nombre total de choix faits : {self.nombres_choix_faits}")
        print("Choix par étape :")
        for etape, choix in self.choix_par_etape.items():
            print(f"- {etape}: {choix}")

class ModuleGraphismes:
    @staticmethod
    def afficher_graphismes(graphismes):
        for ligne in graphismes:
            print(ligne)
            time.sleep(0.5)  # Ajout d'une pause pour une meilleure lisibilité

def afficher_description(description):
    print(description)
    time.sleep(1)

def demander_choix(choix_possibles):
    choix_utilisateur = input("Choisissez parmi {} : ".format(", ".join(choix_possibles)))
    return choix_utilisateur

def jouer_histoire(etape_initiale, gestionnaire_choix, statistiques_suivi, module_graphismes):
    etape_actuelle = etape_initiale

    while True:
        # Afficher les graphismes
        module_graphismes.afficher_graphismes(etape_actuelle.graphismes)

        # Afficher la description de l'étape
        afficher_description(etape_actuelle.description)

        # Afficher les choix disponibles
        choix_possibles = etape_actuelle.choix.keys()
        choix_utilisateur = demander_choix(choix_possibles)

        # Valider le choix
        if gestionnaire_choix.valider_choix(choix_possibles, choix_utilisateur):
            etape_actuelle = etape_actuelle.choix[choix_utilisateur]
            statistiques_suivi.enregistrer_choix(etape_actuelle, choix_utilisateur)
        else:
            print("Choix invalide. Veuillez choisir à nouveau.")

# ... (les importations et les classes précédentes restent inchangées)

if __name__ == "__main__":
    # Création des étapes
    etape1 = Etape("Vous êtes dans une forêt sombre. Choisissez votre direction.")
    etape1.graphismes = text2art("Forêt")

    etape2a = Etape("Vous trouvez une clairière ensoleillée.", ["   🌳🌳🌳🌳🌳   ",
                                                                " 🌲       🌲 ",
                                                                "🌳         🌳",
                                                                "🌲         🌲",
                                                                " 🌳       🌳 ",
                                                                "   🌳🌳🌳🌳🌳   "], {"Aller vers le nord": Etape("Vous trouvez une rivière.")})

    etape2b = Etape("Vous vous perdez dans les bois.", ["    🌲🌲🌲🌲🌲    ",
                                                         "  🌲🌳      🌲🌲  ",
                                                         " 🌲          🌲 ",
                                                         "🌳            🌳",
                                                         " 🌲          🌲 ",
                                                         "  🌲🌳      🌲🌲  ",
                                                         "    🌲🌲🌲🌲🌲    "], {"Retourner en arrière": etape1, "Continuer à errer": Etape("Vous trouvez une grotte sombre.")})

    etape3a = Etape("Vous pêchez dans la rivière.", ["    ~~~~~~    ",
                                                      "   ~      ~   ",
                                                      "  ~   🎣   ~  ",
                                                      "   ~      ~   ",
                                                      "    ~~~~~~    "], {"Rester à pêcher": Etape("Vous attrapez un poisson."), "Partir à l'aventure": Etape("Vous trouvez un village.")})
    
    etape3b = Etape("Vous explorez la grotte.", ["  _______  ",
                                                 " /       \\",
                                                 "/         \\",
                                                 "\\         /",
                                                 " \\_______/"], {"Continuer plus profondément": Etape("Vous trouvez un trésor."), "Rebrousser chemin": etape1})

    etape4a = Etape("Vous arrivez au village.", ["    🏡🏡🏡    ",
                                                  "   |     |   ",
                                                  "   |     |   ",
                                                  "   |_____|   ",
                                                  "              ",
                                                  "Bienvenue au village ! Choisissez votre prochaine action."],
                    {"Visiter l'auberge": Etape("Vous rencontrez un mystérieux étranger."),
                     "Explorer la place du marché": Etape("Vous découvrez une boutique magique.")})
    
    etape4b = Etape("Vous ouvrez le trésor.", ["  💰💰💰💰💰  ",
                                               " 💎      💎 ",
                                               "💰          💰",
                                               "💰   TREASURE💰",
                                               " 💎      💎 ",
                                               "  💰💰💰💰💰  "],
                    {"Obtenir une épée magique": Etape("Vous êtes prêt pour l'aventure !"),
                     "Libérer un monstre": Etape("Le monstre vous attaque. Fin de l'aventure.")})

    etape5a = Etape("Vous discutez avec l'étranger mystérieux.", ["   🤖🤖🤖   ",
                                                                  "   |   |    ",
                                                                  "   |   |    ",
                                                                  "   |   |    ",
                                                                  "  /     \\   ",
                                                                  "Bienvenue, aventurier. J'ai une quête pour vous.",
                                                                  "Acceptez-vous ?"],
                    {"Accepter sa quête": Etape("Une nouvelle aventure commence."),
                     "Refuser et partir": etape1})

    # ... (définir les choix possibles entre les étapes)

    # Créer un gestionnaire de choix
    gestionnaire_choix = GestionnaireChoix()

    # Créer un gestionnaire de statistiques et de suivi
    statistiques_suivi = StatistiquesSuivi()

    # Créer un module de graphismes
    module_graphismes = ModuleGraphismes()

    # Commencer l'histoire
    jouer_histoire(etape1, gestionnaire_choix, statistiques_suivi, module_graphismes)

    # Afficher les statistiques à la fin
    statistiques_suivi.afficher_statistiques()

