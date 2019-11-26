# UnityNgui_Skilltree
__Only Video (Commerical Ngui Asset)__
This Project is Ngui Skilltree with Editor


------------  

## Vedio Link

https://youtu.be/Euj89naSp5I
------------  
## Important code(script)
__Skill Check & Take Script__
```c#
   //스킬 트리 내에서 검사
    public void CheckSkilltree(Color p_UnlockColor,Color p_LockColor,Color p_CanUnlockColor)
    {
       
        if(SkillTreeReader.Instance.IsSkillUnlocked(m_SkillId))
        {
            Debug.Log("성공");
            m_SpriteBtn.color = p_UnlockColor;
            m_Collider.enabled = false;

        }
        else if(!SkillTreeReader.Instance.CanSkillBeUnlocked(m_SkillId))
        {
            //버튼 비활성화 (잠겨있는 스킬일떄)
            Debug.Log("잠김");
            m_SpriteBtn.color = p_LockColor;
            m_Collider.enabled = false;
        }
        else
        {
            //unlock이 가능한 스킬일떄
            Debug.Log("언락가능");
            m_SpriteBtn.color = p_CanUnlockColor;
            m_Collider.enabled = true;
        }
    }
    public void TakeSkill()
    {
        if(SkillTreeReader.Instance.UnlockSkill(m_SkillId))
        {
            m_skillList.Refresh(); // 스킬트리 새로고침
            //SkillTreeReader.Instance.availablePoints
           // PlayerPrefs.SetInt("Score",SkillTreeReader.Instance.an)
        }
    }
```
__SkillTree Save Script__
```c#
// 스킬트리 저장
    private void SaveSkillTree()
    {
        if (nodes.Count > 0)
        {

            skillTree.skilltree = new Skill[nodes.Count];
            int[] dependencies;
            List<int> dependenciesList = new List<int>();

            for (int i = 0; i < nodes.Count; ++i)
            {
                if (connections != null)
                {
                    List<Connection> connectionsToRemove = new List<Connection>();
                    List<ConnectionPoint> connectionsPointsToCheck = new List<ConnectionPoint>();

                    for (int j = 0; j < connections.Count; j++)
                    {
                        if (connections[j].inPoint == nodes[i].inPoint)
                        {
                            for (int k = 0; k < nodes.Count; ++k)
                            {
                                if (connections[j].outPoint == nodes[k].outPoint)
                                {
                                    dependenciesList.Add(k);
                                    break;
                                }
                            }
                            connectionsToRemove.Add(connections[j]);
                            connectionsPointsToCheck.Add(connections[j].outPoint);
                        }
                    }
                }
                dependencies = dependenciesList.ToArray();
                dependenciesList.Clear();
                skillTree.skilltree[i] = nodes[i].skill;
                skillTree.skilltree[i].skill_Dependencies = dependencies;
            }

            string json = JsonUtility.ToJson(skillTree);
            string path = null;

            //해당 경로에 jason 파일 저장하기
            path = "Assets/Resources/Data/skilltree.json";

            using (FileStream fs = new FileStream(path, FileMode.Create))
            {
                using (StreamWriter writer = new StreamWriter(fs))
                {
                    writer.Write(json);
                }
            }
            UnityEditor.AssetDatabase.Refresh();

            SaveNodes();
        }
    }

    // 노드 저장
    private void SaveNodes()
    {
        NodeDataCollection nodeData = new NodeDataCollection();
        nodeData.nodeDataCollection = new NodeData[nodes.Count];

        for (int i = 0; i < nodes.Count; ++i)
        {
            nodeData.nodeDataCollection[i] = new NodeData();
            nodeData.nodeDataCollection[i].id_Node = nodes[i].skill.id_Skill;
            nodeData.nodeDataCollection[i].position = nodes[i].rect.position;
        }

        string json = JsonUtility.ToJson(nodeData);
        string path = "Assets/Resources/Data/nodeData.json";

        using (FileStream fs = new FileStream(path, FileMode.Create))
        {
            using (StreamWriter writer = new StreamWriter(fs))
            {
                writer.Write(json);
            }
        }
        UnityEditor.AssetDatabase.Refresh();
    }
```
__Skill Unlock Script__
```c#
//스킬 언락 메소드
    public bool UnlockSkill(int id_Skill)
    {
        if(skillDic.TryGetValue(id_Skill, out currentSkill))
        {
            //어빌리티포인트가 코스트 보다 높다면 true, 적다면 false
            if (currentSkill.cost <= availablePoint)
            {
                availablePoint -= currentSkill.cost;
                currentSkill.unlocked = true;

                // 딕셔너리에 재배치
                skillDic.Remove(id_Skill);
                skillDic.Add(id_Skill, currentSkill);

                return true;
            }
            else
            {
                return false;   
            }
        }
        else
        {
            return false; 
        }
    }
```


  ------------
## How to Play

ClicK Skill tree
Passvie || Active
