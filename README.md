#include <stdio.h>
#include <stdbool.h>

// 定义电梯状态
typedef enum {
    IDLE,       // 空闲状态
    UP,         // 上行状态
    DOWN        // 下行状态
} ElevatorStatus;

// 定义电梯结构体
typedef struct {
    int currentFloor;       // 当前楼层
    ElevatorStatus status;  // 电梯状态
    bool floors[12];        // 每层楼是否有请求
} Elevator;

// 初始化电梯
void initElevator(Elevator* elevator) {
    elevator->currentFloor = 1;
    elevator->status = UP;
    for (int i = 0; i < 12; i++) {
        elevator->floors[i] = false;
    }
}

// 在指定楼层设置请求
void setRequest(Elevator* elevator, int floor) {
    elevator->floors[floor - 1] = true;
}

// 判断电梯是否需要改变方向
bool needChangeDirection(Elevator* elevator) {
    if (elevator->status == UP) {
        for (int i = elevator->currentFloor; i < 12; i++) {
            if (elevator->floors[i]) {
                return false;
            }
        }
        return true;
    }
    else if (elevator->status == DOWN) {
        for (int i = elevator->currentFloor - 2; i >= 0; i--) {
            if (elevator->floors[i]) {
                return false;
            }
        }
        return true;
    }
    return false;
}

// 根据当前楼层和电梯状态选择下一个目标楼层
int selectTargetFloor(Elevator* elevator) {
    if (elevator->status == UP|| elevator->status == IDLE) {
        for (int i = elevator->currentFloor; i < 12; i++) {
            if (elevator->floors[i]) {
                return i + 1;
            }
        }
    }
    else if (elevator->status == DOWN || elevator->status == IDLE) {
        for (int i = elevator->currentFloor - 2; i >= 0; i--) {
            if (elevator->floors[i]) {
                return i + 1;
            }
        }
    }
    return elevator->currentFloor;
}

// 运行电梯
void runElevator(Elevator* elevator) {
    while (1) {
        if (elevator->floors[elevator->currentFloor - 1]) {
            printf("Elevator arrived at floor %d\n", elevator->currentFloor);
            elevator->floors[elevator->currentFloor - 1] = false;
        }

        if (needChangeDirection(elevator)) {
            if (elevator->status == UP) {
                elevator->status = DOWN;
            }
            else if (elevator->status == DOWN) {
                elevator->status = UP;
            }
        }

        int targetFloor = selectTargetFloor(elevator);
        if (targetFloor > elevator->currentFloor) {
            elevator->currentFloor++;
            printf("Elevator going up to floor %d\n", targetFloor);
            printf("Elevator is at floor %d\n", elevator->currentFloor);
            elevator->status = UP;
        }
        else if (targetFloor < elevator->currentFloor) {
            elevator->currentFloor--;
            printf("Elevator going down to floor %d\n", targetFloor);
            printf("Elevator is at floor %d\n", elevator->currentFloor);
            elevator->status = DOWN;
        }
        else {
            elevator->status = IDLE;
            printf("Elevator is idle at floor %d\n", elevator->currentFloor);
        }

        break;
    }
}

int main() {
    // 创建三部电梯
    Elevator elevators[3];
    for (int i = 0; i < 3; i++) {
        initElevator(&elevators[i]);
    }
    //接受指令
    while (1) {
        while (1)
        {
            int add_request = 0;
            printf("Input 1 to add a passenger or 0 to continue\n");
            scanf("%d", &add_request);
            if (add_request)
            {
                int target_floor = 0,passenger_floor=0;
                printf("Please input your current floor:\n");
                scanf("%d",&passenger_floor);
                printf("Please input your target floor:\n");
                scanf("%d", &target_floor);
                bool passenger_up = passenger_floor < target_floor;
                int distance[3],up_min=12,down_min=-12,up_count=0,down_count=0;
                for (int i = 0; i < 3; i++) { distance[i] = passenger_floor - elevators[i].currentFloor; }
                for (int i = 0; i < 3; i++) 
                {
                    if (distance[i] >= 0 && distance[i] < up_min&&(elevators[i].status==UP|| elevators[i].status == IDLE)) { up_min = distance[i]; up_count = i; }
                    if (distance[i] <= 0 && distance[i] > down_min && (elevators[i].status == DOWN|| elevators[i].status == IDLE)) { down_min = distance[i]; down_count = i; }
                }
                if (passenger_up)
                   { 
                    setRequest(&elevators[up_count],target_floor);
                   }
                else{ setRequest(&elevators[down_count], target_floor); }
            }
            else { break; }
        }

        // 运行电梯
        for (int i = 0; i < 3; i++) {
            printf("Running Elevator %d:\n", i + 1);
            runElevator(&elevators[i]);
            printf("\n");
        }
    }

}
