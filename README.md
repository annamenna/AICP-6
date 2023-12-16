# AICP-6
#include <iostream>
#include <vector>

using namespace std;

// Constants for weight limits
const double CEMENT_MIN_WEIGHT = 24.9;
const double CEMENT_MAX_WEIGHT = 25.1;
const double GRAVEL_SAND_MIN_WEIGHT = 49.9;
const double GRAVEL_SAND_MAX_WEIGHT = 50.1;

// Constants for sack prices
const int CEMENT_PRICE = 3;
const int GRAVEL_PRICE = 2;
const int SAND_PRICE = 2;
const int SPECIAL_PACK_PRICE = 10;

string checkSack(char contents, double weight) {
    if (contents != 'C' && contents != 'G' && contents != 'S') {
        return "Rejected: Invalid contents";
    }

    if (contents == 'C' && !(CEMENT_MIN_WEIGHT < weight && weight < CEMENT_MAX_WEIGHT)) {
        return "Rejected: Invalid weight for cement sack";
    }

    if ((contents == 'G' || contents == 'S') && !(GRAVEL_SAND_MIN_WEIGHT < weight && weight < GRAVEL_SAND_MAX_WEIGHT)) {
        return "Rejected: Invalid weight for gravel or sand sack";
    }

    return "Accepted: Contents - " + string(1, contents) + ", Weight - " + to_string(weight) + " kg";
}

pair<double, int> checkOrder(const vector<pair<char, double>>& order) {
    double totalWeight = 0;
    int rejectedSacks = 0;

    for (const auto& sack : order) {
        char contents = sack.first;
        double weight = sack.second;

        string result = checkSack(contents, weight);

        if (result.find("Accepted") != string::npos) {
            totalWeight += weight;
        } else {
            rejectedSacks++;
        }

        cout << result << endl;
    }

    cout << "Total weight of the order: " << totalWeight << " kg" << endl;
    cout << "Number of sacks rejected: " << rejectedSacks << endl;

    return {totalWeight, rejectedSacks};
}

void calculatePrice(const vector<pair<char, double>>& order) {
    int regularPrice = 0;
    int specialPacks = 0;

    for (const auto& sack : order) {
        char contents = sack.first;

        if (contents == 'C') {
            regularPrice += CEMENT_PRICE;
        } else if (contents == 'G') {
            regularPrice += GRAVEL_PRICE;
        } else if (contents == 'S') {
            regularPrice += SAND_PRICE;
        }
    }

    for (const auto& sack : order) {
        char contents = sack.first;

        if (contents == 'C' && order.count({'C', 1}) > 0 && order.count({'S', 2}) > 0 && order.count({'G', 2}) > 0) {
            specialPacks = min({order.count({'C', 1}), order.count({'S', 2}), order.count({'G', 2})});
            break;
        }
    }

    int discountPrice = regularPrice - (specialPacks * SPECIAL_PACK_PRICE);
    int amountSaved = specialPacks * (regularPrice - discountPrice);

    cout << "Regular price for the order: $" << regularPrice << endl;

    if (specialPacks > 0) {
        cout << "Discount price for special packs: -$" << amountSaved << endl;
        cout << "New price for the order: $" << discountPrice << endl;
    }
}

int main() {
    // Example usage
    vector<pair<char, double>> order = {{'C', 25.0}, {'G', 50.0}, {'S', 49.5}};
    
    cout << "TASK 1 - Check the contents and weight of a single sack" << endl;
    for (const auto& sack : order) {
        cout << checkSack(sack.first, sack.second) << endl;
    }

    cout << "\nTASK 2 - Check a customer's order for delivery" << endl;
    auto result = checkOrder(order);

    cout << "\nTASK 3 - Calculate the price for a customer's order" << endl;
    calculatePrice(order);

    return 0;
}
