#### Firebase Examples

#### collection query with filter
```ts

import { Employee, FbCollection } from '@@app/types/types';
import { useQuery, UseQueryOptions } from '@tanstack/react-query';
import {
  collection,
  getDocs,
  limit,
  orderBy,
  query,
  QueryConstraint,
  where,
} from 'firebase/firestore';
import { db } from '../client-initialize-firebase';

interface FetchEmployeesProps extends Partial<Employee> {
  not_ocr_full_name?: string;
}

const fetchEmployees = async (props: FetchEmployeesProps = {}) => {
  const queryConstraints: QueryConstraint[] = [];

  const { ocr_full_name, not_ocr_full_name } = props;

  if (ocr_full_name) queryConstraints.push(where('ocr_full_name', '==', ocr_full_name));
  if (not_ocr_full_name) queryConstraints.push(where('ocr_full_name', '!=', not_ocr_full_name));

  queryConstraints.push(orderBy('crated_at', 'desc'));
  queryConstraints.push(limit(10));

  const q = query(collection(db, FbCollection.employees), ...queryConstraints);

  const querySnapshot = await getDocs(q);

  const results: Employee[] = [];

  querySnapshot.forEach((doc) => {
    const employees = { id: doc.id, ...doc.data } as Employee;
    results.push(employees);
  });

  return results;
};

export const useFetchEmployees = (
  props: FetchEmployeesProps,
  options?: UseQueryOptions<Employee[]>
) => {
  return useQuery({
    queryKey: ['useFetchEmployees', props],
    queryFn: () => fetchEmployees(props),
    ...options,
  });
};
```
