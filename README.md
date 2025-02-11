# bip39-recovery-tool
The incredible miner.

import argparse
from mnemonic import Mnemonic
from itertools import product

def validate_mnemonic(seed_phrase):
    """Check if a given mnemonic seed phrase is valid"""
    mnemo = Mnemonic("english")
    return mnemo.check(seed_phrase)

def generate_possible_mnemonics(partial_phrase):
    """Attempt to reconstruct a valid mnemonic seed if partially forgotten"""
    mnemo = Mnemonic("english")
    words = mnemo.wordlist
    possible_phrases = []

    split_phrase = partial_phrase.split()
    missing_count = split_phrase.count("...")  # Assume missing words are replaced by "..."
    
    if missing_count == 0:
        return [partial_phrase] if validate_mnemonic(partial_phrase) else []

    for possible_words in product(words, repeat=missing_count):
        temp_phrase = partial_phrase
        for word in possible_words:
            temp_phrase = temp_phrase.replace("...", word, 1)
        
        if validate_mnemonic(temp_phrase):
            possible_phrases.append(temp_phrase)

    return possible_phrases

def main():
    parser = argparse.ArgumentParser(description="BIP39 Mnemonic Recovery Tool")
    parser.add_argument("--validate", type=str, help="Check if a mnemonic seed phrase is valid")
    parser.add_argument("--recover", type=str, help="Recover missing words in a mnemonic seed phrase")
    
    args = parser.parse_args()
    
    if args.validate:
        if validate_mnemonic(args.validate):
            print("Valid Mnemonic Seed Phrase!")
        else:
            print("Invalid Mnemonic Seed Phrase!")
    
    if args.recover:
        valid_phrases = generate_possible_mnemonics(args.recover)
        if valid_phrases:
            print("Possible Valid Mnemonics:")
            for phrase in valid_phrases:
                print(phrase)
        else:
            print("No valid mnemonics found. Try again with more known words.")

if __name__ == "__main__":
    main()
