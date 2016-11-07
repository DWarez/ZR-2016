//Begin page Approach&Co.
void approachPack(float point[3], float distance){
    getMyPos(myPos);
    float virtual1[3];
    float alpha, beta;
    float a, b, c, d, e, f;

    assign(virtual1, point[0], myPos[1], point[2]);
    a = dist(point, virtual1);
    b = dist(myPos, virtual1);
    c = dist(myPos, point);
    

    alpha = acosf((b*b+c*c-a*a)/(2*b*c));
    beta = acosf((a*a+c*c-b*b)/(2*a*c));
    
    DEBUG(("ALPHA: %f", alpha));
    DEBUG(("A: %f", a));
    DEBUG(("B: %f", b));
    DEBUG(("C: %f", c));
    d = (distance*sinf(alpha)/sinf(3.1415/2))/2;
    e = (distance*sinf(beta)/sinf(3.1415/2))/2;
    
    assign(virtual1, myPos[0], myPos[1], point[2]);
    
    a = dist(point, virtual1);
    b = dist(myPos, virtual1);
    c = dist(myPos, point);
    

    alpha = acosf((b*b+c*c-a*a)/(2*b*c));
    beta = acosf((a*a+c*c-b*b)/(2*a*c));

    
    f = (distance*sinf(alpha)/sinf(3.1415/2))/2;

    switch(quadrant(myPos, point)){
        case 2:
        case -2:
            e*=-1;
            break;
        case 3:
        case -3:
            d*=-1;
            e*=-1;
            break;
        case 4:
        case -4:
            d*=-1;
            break;
            
    }
    if(quadrant(myPos, point) < 0)
        f*=-1;
    
    DEBUG(("D: %f", d));
    assign(virtualTarget, point[0] + d, point[1] + e, point[2] + f);
}

int quadrant(float obj[3], float ref[3]){
    int i;
    i = 0;
    if(obj[0] > ref[0] && obj[1] > ref[1])
        i = 1;
    else if(obj[0] > ref[0] && obj[1] < ref[1])
        i = 2;
    else if(obj[0] < ref[0] && obj[1] < ref[1])
        i = 3;
    else if(obj[0] < ref[0] && obj[1] > ref[1])
        i = 4;
    if(obj[2] > ref[2])
        return i;
    else
        return(i*=-1);
}

void rotateToPoint(float target[3]){
    float temp[3];
    getMyPos(myPos);
    mathVecSubtract(temp, target, myPos, 3);
    mathVecNormalize(temp, 3);
    api.setAttitudeTarget(temp);
}

//End page Approach&Co.
//Begin page Misc
void copyArray(float *src, float *dest, int inPos, int dim){
    for(int i = inPos;i < dim;i++)
        dest[i]=src[i];
}

bool compareVector(float a[], float b[], float approx){
    if  (((a[0] < (b[0]+approx))&&(a[0] > (b[0]-approx))) &&
        ((a[1] < (b[1]+approx))&&(a[1] > (b[1]-approx))) &&
        ((a[2] < (b[2]+approx))&&(a[2] > (b[2]-approx))))
        return true;
    else
        return false;
}

void assign(float vec[], float x, float y, float z){
    vec[0]=x;
    vec[1]=y;
    vec[2]=z;
}

float dist(float myPos[], float targetPos[]){
    float tempVec[3];
	mathVecSubtract(tempVec,targetPos,myPos,3);
	return (mathVecMagnitude(tempVec,3));
}


bool compare(float dist, float min, float max){
    if(dist >= min && dist <= max )
        return true;
    else
        return false;
}
//End page Misc
//Begin page Move
void goToPos(float target[3]){
    api.setPositionTarget(target);
}
void brake(){
    float myVel[3];
    api.getMyZRState(myState);
    myVel[0] = (myState[3])*-1.7;
    myVel[1] = (myState[4])*-1.7;
    myVel[2] = (myState[5])*-1.7;
    api.setForces(myVel);
}
//End page Move
//Begin page Packs
void worthyPack() {
    for(int i = 0; i < 4; i++) {
        getRank(i);
    }
    getWorthyPackInfo();
    setDist();
    approachPack(actualTarget, distFromTarget);
}

int itemStat(int num) {
    if(game.itemInZone(num))
        return 3;
        
    if(packInTheirZone(num) && game.hasItem(num) == 0)
        return 4;
        
    return(game.hasItem(num));
}

void getRank(int num) {
    float target[3];
    getMyPos(myPos);
    game.getItemLoc(target, num);
    ranking[num] = 1/(dist(myPos, target));
    switch(num){
        case 0:   //large
        case 1:
            ranking[num]*=4;
            break;
        case 2:   //medium
        case 3:
            ranking[num]*=2.75;
            break;
    }
    switch(itemStat(num)){
        case 0:
            ranking[num]*=1;
            break;
        case 4:
            ranking[num]*=1.75;
            break;
        default:
            ranking[num]*=-100;
            break;
    }
}


void getWorthyPackInfo(){
    targetNumber = 0;
    float max = ranking[0];
    for(int i = 0; i < 4; i++){
        if(ranking[i] > max){
            targetNumber = i;
            max = ranking[i];
        }
    }
    game.getItemLoc(actualTarget, targetNumber);
}

void setDist(){
    switch(targetNumber){
        case 0:
        case 1:
            distFromTarget = .182; 
            distMin = 0.151;
            distMax = 0.173;
            break;
        case 2:
        case 3:
            distFromTarget = .1725;
            distMin = 0.138;
            distMax = 0.160;
            break;
    }
}
//End page Packs
//Begin page Position
char ourColor(){
    getMyPos(myPos);
    if(myPos[1] > 0)
        return 'B';
    else
        return 'R';
}

void zoneInfo(){
    float zoneData[4];
    game.getZone(zoneData);
    assign(ourZone, zoneData[0], zoneData[1], zoneData[2]);
    assign(theirZone, zoneData[0]*(-1), zoneData[1]*(-1), zoneData[2]*(-1));
}

bool packInZone(){
    float temp[3];
    game.getItemLoc(temp, targetNumber);
    if(compareVector(temp, ourZone, 0.03))
        return true;
    else 
        return false; 
}

bool packInTheirZone(int id){
    float temp[3];
    game.getItemLoc(temp, id);
    if(compareVector(temp, theirZone, 0.08))
        return true;
    else
        return false;
}

void getMyPos(float x[3]) {
    api.getMyZRState(myState);
    copyArray(myState, x, 0, 3);
}

/*bool weGotThis(float point[3]){
    return(compareVector(myPos, point, 0.15));
}*/
//End page Position
//Begin page main
//-59,-31,-60

float   myState[12];    //status
float   myPos[3];

float   virtualTarget[3];  //target information
float   actualTarget[3];
float   distFromTarget;
int     targetNumber;
float   distMin;
float   distMax;

float   ranking[4];

float   ourZone[3];     //assembly zones
float   theirZone[3];

char    index;    //switch index
bool    check;

void init(){
    if (ourColor() == 'B'){
        assign(actualTarget, -0.65, 0.65, 0.0);
        assign(virtualTarget, 0.0, 0.5, 0.2);
        targetNumber = 0; 
    }
    else{
        assign(actualTarget, 0.65, -0.65, 0.0);
        assign(virtualTarget, 0.0, -0.5, -0.2);
        targetNumber = 1;
    }
    index = 'f';
    game.dropSPS();
    check = true;
}

void loop(){
    getMyPos(myPos);
    switch(index){
        case 'f':
            if(!compareVector(myPos, actualTarget, 0.15))
                goToPos(actualTarget);
            else{
                game.dropSPS();
                if(check){
                    copyArray(virtualTarget, actualTarget, 0, 3);
                    check = false;
                }
                else{
                    zoneInfo();
                    index = 'w';
                }
            }
            break;
        case 'w':
            worthyPack();
            index = 'p';
            break;
        case 'p':
            if(game.hasItem(targetNumber) == 2)
                index = 'w';
            rotateToPoint(actualTarget);
            if(compare(dist(myPos, actualTarget), distMin, distMax)){
                brake();
                if(game.dockItem(targetNumber) && game.hasItem(targetNumber) == 1){
                    approachPack(ourZone, 0.135);
                    index = '3';
                }
            }
            else
                if(dist(myPos, actualTarget) < distMin){
                    copyArray(actualTarget, virtualTarget, 0, 3);
                    virtualTarget[2]+=0.2;
                }
                goToPos(virtualTarget);
            break;
        case '3':
            //DEBUG(("%f %f %f", virtualTarget[0], virtualTarget[1], virtualTarget[2]));
            rotateToPoint(ourZone);
            goToPos(virtualTarget);
            if(dist(myPos, ourZone) < 0.2){
                copyArray(ourZone, virtualTarget, 0, 3);
                virtualTarget[2]+=0.2;
            }
                
            if(packInZone()){
                DEBUG(("PACKINZONE"));
                brake();
                game.dropItem();
                index = 'w';
            }
            break;
        }
}

//End page main
