# todo_AsyncStorage

````Js
import {
  Text,
  SafeAreaView,
  StyleSheet,
  View,
  TextInput,
  Button,
  FlatList,
} from 'react-native';
import { Card } from 'react-native-paper';
import AssetExample from './components/AssetExample';
import { useState, useEffect } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function App() {
  const [input, setInput] = useState('');
  const [todoList, setTodoList] = useState([]);

  useEffect(() => {
    const loaddata = async () => {
      try {
        const getData = await AsyncStorage.getItem('todo');
        if (getData) {
          setTodoList(JSON.parse(getData));
        }
      } catch (err) {
        console.log('Error while fetching data', err);
      }
    };

    loaddata();
  }, []);

  const saveTodo = async () => {
    if (input.trim() == '') return;

    const data = {
      id: Date.now().toString(),
      title: input,
      status: 'Penidng',
    };

    const newList = [data, ...todoList];

    setTodoList(newList);
    setInput('');

    try {
      await AsyncStorage.setItem('todo', JSON.stringify(newList));
    } catch (err) {
      console.log('Cant save data', err);
    }

    console.log(todoList);
  };

  const removeData = async (id) => {
    const updated = todoList.filter((item) => item.id !== id);

    setTodoList(updated);

    try {
      await AsyncStorage.setItem('todo', JSON.stringify(updated));
    } catch (err) {
      console.log('Error while removing', err);
    }
  };

  const editData = async (id) => {
    const updated = todoList.map((item) =>
      item.id === id
        ? {...item,status: item.status === 'Penidng' ? 'Completed' : 'Penidng' }
        : item
    );

    setTodoList(updated);

    try {
      await AsyncStorage.setItem('todo', JSON.stringify(updated));
    } catch (err) {
      console.log('Error while removing', err);
    }
  };

  const remove = async () => {
    try {
      await AsyncStorage.removeItem('todo');
      setTodoList([]);
    } catch (err) {
      console.log('Error while removing', err);
    }
  };

  return (
    <SafeAreaView style={styles.container}>
      <View style={{ flexDirection: 'row' }}>
        <TextInput
          value={input}
          placeholder={'Enter Value'}
          style={{ width: 200, height: 50, borderWidth: 1 }}
          onChangeText={setInput}
        />
        <Button title="add" onPress={saveTodo} />
        <Button title="Remove All" onPress={remove} />
      </View>
      <FlatList
        data={todoList || []}
        keyExtractor={(item) => item?.id}
        renderItem={({ item }) => {
          return (
            <View style={{ paddingVertical: 5 }}>
              <Text style={{ fontSize: 15, fontWeight: '500' }}>
                {item?.title}
              </Text>
              <Text style={{ fontSize: 15, fontWeight: '500' }}>
                {item?.status}
              </Text>
              <View style={{ flexDirection: 'row', gap: 5 }}>
                <Button
                  title="delete"
                  onPress={() => {
                    removeData(item?.id);
                  }}
                />
                <Button
                  title="Edit"
                  onPress={() => {
                    editData(item?.id);
                  }}
                />
              </View>
            </View>
          );
        }}
      />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#ecf0f1',
    padding: 8,
  },
  paragraph: {
    margin: 24,
    fontSize: 18,
    fontWeight: 'bold',
    textAlign: 'center',
  },
});

```
