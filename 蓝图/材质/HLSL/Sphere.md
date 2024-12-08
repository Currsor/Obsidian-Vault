struct RMstruct  
{  
    float CopySign(float A, float B)  
    {       if (B >= 0.0)  
       {          return abs(A);  
       }       else  
       {  
          return -abs(A);  
       }    }    float RMbox(float3 Position, float3 HalfSize)    
    {  
       float3 q = abs(Position) - HalfSize;    
return length(max(q,0.0)) + min(max(q.x,max(q.y,q.z)),0.0);    
}    
    float3 RMNormal_Box(float3 Position, float3 HalfSize, float3 ObjectPosition)    
    {  
       float3 q = abs(Position) - HalfSize;    
float3 normal = float3(0, 0, 0);    
float epsilon = 0.0001;    
if (q.x < epsilon)    
          normal.x = CopySign(1, Position.x - ObjectPosition.x);    
if (q.y < epsilon)    
          normal.y = CopySign(1, Position.y - ObjectPosition.y);    
if (q.z < epsilon)    
          normal.z = CopySign(1, Position.z - ObjectPosition.z);    
    
return normalize(normal);    
} };  
  
RMstruct RM;  
float4 Color = 0;  
float3 Position = WorldPosition;  
float3 Normal = 0;  
  
for (int i = 0; i < MaxSteps; i++)  
{  
    if (SceneDepth < length(Position - CameraPosition))  
       break;  
    // 使用盒子距离函数  
    float Distance = RM.RMbox(Position - ObjectPosition, Radius.xxx);  
  
    if (Distance < 0.0001)    
    {  
       Color = 1;    
Normal = RM.RMNormal_Box(Position - ObjectPosition, Radius.xxx, ObjectPosition);    
break;    
    }    
Position += CameraVector * Distance; }  
  
return float4(Normal, Color.a);