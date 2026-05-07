#include <iostream>

enum class State { OFF, COOL, HEAT, FAN_ONLY };
enum class Event { POWER, MODE };

class AirConditioner {
private:
    State current;

    std::string stateToString(State s) const {
        switch (s) {
            case State::OFF: return "OFF";
            case State::COOL: return "COOL";
            case State::HEAT: return "HEAT";
            case State::FAN_ONLY: return "FAN_ONLY";
            default: return "UNKNOWN";
        }
    }

public:
    AirConditioner() : current(State::OFF) {}

    void handle(Event event) {
        std::cout << "Current: " << stateToString(current)
                  << " | Event: " << (event == Event::POWER ? "POWER" : "MODE")
                  << " -> ";

        switch (current) {
            case State::OFF:
                if (event == Event::POWER) {
                    current = State::FAN_ONLY;
                    std::cout << "FAN_ONLY (Power ON)\n";
                } else {
                    std::cout << "No action\n";
                }
                break;

            case State::FAN_ONLY:
                if (event == Event::POWER) {
                    current = State::OFF;
                    std::cout << "OFF (Power OFF)\n";
                } else if (event == Event::MODE) {
                    current = State::COOL;
                    std::cout << "COOL (Cooling, 18°C)\n";
                }
                break;

            case State::COOL:
                if (event == Event::POWER) {
                    current = State::OFF;
                    std::cout << "OFF (Power OFF)\n";
                } else if (event == Event::MODE) {
                    current = State::HEAT;
                    std::cout << "HEAT (Heating, 25°C)\n";
                }
                break;

            case State::HEAT:
                if (event == Event::POWER) {
                    current = State::OFF;
                    std::cout << "OFF (Power OFF)\n";
                } else if (event == Event::MODE) {
                    current = State::FAN_ONLY;
                    std::cout << "FAN_ONLY (Fan only)\n";
                }
                break;
        }
    }

    State getCurrentState() const { return current; }
};

int main() {
    AirConditioner ac;

    ac.handle(Event::POWER);  // ON → FAN_ONLY
    ac.handle(Event::MODE);   // → COOL
    ac.handle(Event::MODE);   // → HEAT
    ac.handle(Event::MODE);   // → FAN_ONLY
    ac.handle(Event::POWER);  // OFF

    return 0;
}
